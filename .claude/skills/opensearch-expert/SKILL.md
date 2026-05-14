---
name: opensearch-expert
description: "Experto en generación de queries OpenSearch DSL para el índice de monitoreo de transacciones ISO20022/ISO8583. Usar cuando el usuario pida buscar transacciones, filtrar por estado, agrupar resultados, detectar operaciones sin respuesta, o cualquier consulta relacionada con el índice de monitoreo."
when_to_use: "Activar siempre que se solicite una query, búsqueda, filtro, agregación o análisis sobre OpenSearch. También activar cuando se mencionen términos como: transacción, aprobación, rechazo, rango de fechas, sin respuesta, agrupar, contar, traceData, processingResult, monitoring, peer01, peer02."
---

# OpenSearch Expert — Monitoreo ISO20022

## Contexto acumulado del equipo

Antes de generar cualquier query, leer obligatoriamente estos archivos en orden:

1. `context/queries-probadas.md` — Si existe una query similar, adaptarla en lugar de crear desde cero.
2. `context/valores-descubiertos.md` — Valores de campos encontrados en producción que pueden no estar en el esquema base.
3. `context/errores-comunes.md` — Errores conocidos que se deben evitar al construir la query.
4. `doc/codigos-respuesta.md` — Referencia completa de códigos de respuesta por red. **Leer siempre que la query involucre resultados, rechazos o tipos de error.**
5. `doc/application-data.md` — Referencia de BINs, monedas, MCC y entidades P2P. **Leer cuando la query involucre productos de tarjeta, BIN, tipo de comercio (MCC), moneda o entidades de billetera.**

---

## Códigos de respuesta — Reglas críticas

El resultado de una transacción se puede buscar por **dos campos equivalentes**:

```
processingResult.resultData.otherResult       → nombre del código  (ej: "insufficient_credit_balance")
processingResult.resultData.otherResultDetails → valor ISO8583      (ej: "51")
```

Ambos representan lo mismo. La diferencia es que **un valor ISO8583 puede tener múltiples nombres**. Por ejemplo el código `51` agrupa: `insufficient_credit_balance`, `not_enough_balance`, `insufficient_debit_balance`, `exceeded_limits`, `exceeded_credit_limit`.

### Cuándo usar cada campo

| Caso | Campo recomendado | Por qué |
|------|------------------|---------|
| Usuario dice "saldo insuficiente" | `otherResult` con `terms` (todos los nombres del 51) | Más preciso, captura el subtipo exacto |
| Usuario dice "código 51" | `otherResultDetails: "51"` | Más simple, captura todos los subtipos a la vez |
| Agrupar por tipo de rechazo | `otherResult.keyword` en `terms` agg | Más granular que agrupar por código ISO8583 |
| Usuario menciona red específica | Filtrar por `_index: "peer01"` o `_index: "peer02"` | Los códigos pueden diferir entre redes |

### Por red

- **PEER01 = Visa**
- **PEER02 = Mastercard**

Los mismos nombres de código existen en ambas redes, pero el valor ISO8583 puede ser diferente. Siempre consultar `doc/codigos-respuesta.md` para la red específica.

### Ejemplo: saldo insuficiente en Visa

```json
POST /peer01/_search
{
  "query": {
    "bool": {
      "should": [
        { "term": { "processingResult.resultData.otherResultDetails.keyword": "51" }},
        { "terms": { "processingResult.resultData.otherResult.keyword": [
          "insufficient_credit_balance",
          "insufficient_debit_balance",
          "not_enough_balance",
          "exceeded_limits",
          "exceeded_credit_limit"
        ]}}
      ],
      "minimum_should_match": 1
    }
  }
}
```

---

## Esquema completo del documento

```
_index                                         string   "peer01" | "peer02"
_id                                            string   ID único del documento

_source.networkName                            string   "PEER01" | "PEER02"
_source.messageFunction                        string   "AUTQ"=0100, "FAUQ"=0120 , "FRVA "=0400, "RVRA "=0420 

# ── TRACE DATA (array key/value) ──────────────────────────────────────────
_source.traceData[].key                        string   "PAYMENT_ID"
_source.traceData[].value                      string   UUID de la transacción

# ── MONITORING ────────────────────────────────────────────────────────────
_source.monitoring.binCode                     string   "511578"
_source.monitoring.binDescription             string   "MASTERCARD PLATINUM"
_source.monitoring.merchantNameAceptor        string   nombre del comercio
_source.monitoring.channelFilter              string   "POS" | "ATM" | "OTHER" | "ECOMMERCE"
_source.monitoring.operationFilter            string   "PURCHASE" | "WTHDMON" | "PAYMENT" | "BALANCE INQUIRY"
_source.monitoring.transactionTypeDescription string   "COMPRAS" | "RETIROS" | ...
_source.monitoring.countryDate                datetime ISO8601 con timezone "-05:00" ← campo principal de fecha
_source.monitoring.transactionStatus          string   "Approved" | "Denied" | (ausente=sin respuesta)
_source.monitoring.p2pType                     string   "PLIN" | "YAPE" | null
_source.monitoring.merchantCategoryDescription string   "GASOLINERAS" | "SUPERMERCADOS" | ...

# ── CUSTOM DATA LOCAL ─────────────────────────────────────────────────────
_source.customDataLocal.additionalData[].request.key    string  "MSGTYPE"
_source.customDataLocal.additionalData[].request.value  string  "0100"=auth request, "0120"=advice request, "0400"=reversal request, "0420"=reversal advice request

# ── SOCKET ────────────────────────────────────────────────────────────────
_source.socketPort                             string   "7003"

# ── ADDENDUM DATA (tipo de mensaje de respuesta) ─────────────────────────
_source.addendumData.additionalData[].key      string   "UNSP"
_source.addendumData.additionalData[].value    string   string  "0110"=auth response, "0130"=advice response, "0410"=reversal response, "0430"=reversal advice response

# ── ENVIRONMENT ───────────────────────────────────────────────────────────
_source.environment.terminal.terminalId.id           string  "00000023"
_source.environment.terminal.terminalId.country      string  código país ISO (ej: "840"=USA)
_source.environment.acquirer.id                      string  ID del adquirente "007778"
_source.environment.acquirer.country                 string

_source.environment.sender.id                        string  "001297"
_source.environment.sender.additionalId.key          string  "additionalDataRetailer"
_source.environment.sender.additionalId.value        string  "C8"

_source.environment.acceptor.id                      string  ID del comercio "000498763010887"
_source.environment.acceptor.nameAndLocation         string  nombre + ciudad + país
_source.environment.acceptor.localData.address.postalCode string
_source.environment.card.pan                         string   PAN o numero de tarjeta enmascarado  
_source.environment.issuer.assigner                  string   emisor

# ── CONTEXT ───────────────────────────────────────────────────────────────
_source.context.transactionContext.merchantCategoryCode          string  MCC "3502"
_source.context.transactionContext.merchantCategorySpecificData  string  "NATIONAL" | "INTERNATIONAL"
_source.context.transactionContext.settlementService.settlementServiceDates.settlementDate  string  "MMDD"
_source.context.transactionContext.transactionInitiator          string  "0000"
_source.context.transactionContext.additionalData[].key          string  "ENTRY_MODE"|"OPERATION_TYPE"|"CHANNEL"|"OWNER"
_source.context.transactionContext.additionalData[].value        string  valores según key

# ── TRANSACTION ───────────────────────────────────────────────────────────
_source.transaction.transactionId.transactionReference    string   UUID
_source.transaction.transactionId.transmissionDateTime    datetime fecha de la operacion
_source.transaction.transactionId.systemTraceAuditNumber  string   numero de operacion 
_source.transaction.transactionId.localDateTime           datetime fecha y hora local
_source.transaction.transactionId.retrievalReferenceNumber string  RRN "000011361465"
_source.transaction.transactionType                       string   "00"=compra, "01"=retiro, "10"=AFT , "26"=OCT
_source.transaction.accountFrom.accountType               string   "00"
_source.transaction.accountTo.accountType                 string   "00"

_source.transaction.transactionAmounts.transactionAmount.amount   float   monto original
_source.transaction.transactionAmounts.transactionAmount.currency string  "USD" | "PEN" | ...
_source.transaction.transactionAmounts.cardholderBillingAmount.amount          float
_source.transaction.transactionAmounts.cardholderBillingAmount.currency        string
_source.transaction.transactionAmounts.cardholderBillingAmount.effectiveExchangeRate string

# ── PROCESSING RESULT ─────────────────────────────────────────────────────
_source.processingResult.approvalCode                            string   codigo de aprobacion "000000" equivalente al campo 38 del iso 8583
_source.processingResult.resultData.otherResult                  string   "insufficient_credit_balance" | "card_blocked" | "cancelled_account" | ...
_source.processingResult.resultData.otherResultDetails           string   relacionado al codigo-respuesta.md : "51"=saldo insuficiente
_source.processingResult.additionalInformation[].key             string   "transaction"
_source.processingResult.additionalInformation[].value           string   "Approved" | "Denied"
```

---

## Reglas de generación de queries

### Campos de texto: cuándo usar cada uno
```
# Arrays anidados (additionalData, traceData) → match_phrase obligatorio
"match_phrase": { "addendumData.additionalData.value": "0110" }

# Campos escalares para exact match → term con .keyword
"term": { "monitoring.transactionStatus.keyword": "Approved" }

# Aggregations y prefix → siempre .keyword
"terms": { "field": "processingResult.resultData.otherResult.keyword" }
"prefix": { "processingResult.resultData.otherResult.keyword": "NO_FOUND" }
```

### Fechas — siempre con time_zone
```json
"range": {
  "monitoring.countryDate": {
    "gte": "2026-04-13T00:00:00",
    "lte": "now",
    "time_zone": "-05:00"
  }
}
```

### Atajos de fecha útiles
```
now          → ahora mismo
now-1h       → hace 1 hora
now-24h      → hace 24 horas
now/d        → inicio del día actual
now-1d/d     → inicio del día anterior
```

### Operaciones sin respuesta
```json
"must_not": [{ "exists": { "field": "monitoring.transactionStatus" } }]
```

### Estructura base siempre usar
```json
POST /peer01/_search
{
  "size": 100,
  "query": {
    "bool": {
      "must": [],
      "must_not": [],
      "filter": []
    }
  },
  "sort": [{ "monitoring.countryDate": "desc" }]
}
```

### Aggregations por tiempo
```json
"aggs": {
  "por_hora": {
    "date_histogram": {
      "field": "monitoring.countryDate",
      "calendar_interval": "hour",
      "time_zone": "-05:00"
    }
  },
  "por_resultado": {
    "terms": {
      "field": "processingResult.resultData.otherResult.keyword",
      "size": 20
    }
  }
}
```

---

## Mapeo de lenguaje natural a campos

| El usuario dice | Campo a usar |
|----------------|--------------|
| "aprobadas" | `monitoring.transactionStatus: "Approved"` |
| "denegadas" | `monitoring.transactionStatus: "Denied"` |
| "sin respuesta / timeout / sin estado" | `must_not exists monitoring.transactionStatus` |
| "auth response / 0110" | `addendumData.additionalData` key=UNSP value=0110 |
| "auth request / 0100" | `customDataLocal.additionalData.request` key=MSGTYPE value=0100 |
| "POS / cajero / ecommerce" | `monitoring.channelFilter` |
| "compras / retiros" | `monitoring.operationFilter` |
| "ONUS / mismo banco" | `context...additionalData` key=OWNER value=ONUS |
| "Visa / peer01" | `_index: "peer01"` |
| "Mastercard / peer02" | `_index: "peer02"` |
| "internacionales" | `context.transactionContext.merchantCategorySpecificData: "INTERNATIONAL"` |
| "por RRN" | `transaction.transactionId.retrievalReferenceNumber` |
| "por STAN" | `transaction.transactionId.systemTraceAuditNumber` |
| "por PAYMENT_ID" | `traceData[].key=PAYMENT_ID + traceData[].value=<id>` |
| "por comercio" | `monitoring.merchantNameAceptor` o `environment.acceptor.nameAndLocation` |
| "hoy" | `gte: now/d` |
| "última hora" | `gte: now-1h` |
| "ayer" | `gte: now-1d/d, lte: now/d` |

### Mapeo de términos de negocio a códigos de respuesta

Siempre consultar `doc/codigos-respuesta.md` para obtener los nombres exactos. Referencia rápida de los más frecuentes:

| El usuario dice | otherResultDetails | otherResult (nombres) |
|----------------|-------------------|----------------------|
| "saldo insuficiente / fondos insuficientes" | `51` | `insufficient_credit_balance`, `insufficient_debit_balance`, `not_enough_balance`, `exceeded_limits`, `exceeded_credit_limit` |
| "tarjeta vencida / expirada" | `54` | `expired_card`, `expiration_date_not_valid`, `card_renewal_error`, `card_deactivated` |
| "tarjeta bloqueada / cancelada" | `14`, `62` | `card_blocked`, `canceled_card`, `card_not_active`, `card_disable` |
| "PIN incorrecto" | `55` | `pin_error` |
| "límite excedido / límite diario" | `61` | `exceeded_daily_card_limit`, `exceeded_limit`, `exceeded_limit_withdrawal` |
| "fraude" | `07`, `59` | `card_fraud`, `suspect_fraud`, `fraud_retain` |
| "error de CVV / CVV inválido" | `82` (P01) / `88` (P02) | `cvv2_invalid`, `cvv2_autentication_error`, `cavv_not_valid` |
| "emisor no disponible / sin respuesta del emisor" | `91` | `issuer_not_available`, `deadline_exceeded` |
| "error de sistema" | `96` | `system_error`, `generic_error`, `internal_failure` |
| "no autorizado / operación no permitida" | `57` | `not_authorized`, `user_not_authorized`, `operation_denied` |
| "retiro bloqueado" | `93` (P01) / `57` (P02) | `cash_withdrawal_blocked`, `internet_purchase_blocked` |
| "compra internacional bloqueada" | `93` (P01) / `57` (P02) | `international_purchase_blocked` |

---

## Formato de respuesta

1. Si existe una query similar en `context/queries-probadas.md`, mencionarlo: *"Basado en QPR-001, adapté la query para..."*
2. Entregar la query lista en bloque de código JSON
3. Explicar en 2-3 líneas qué hace
4. Si hay supuestos de fechas, mencionarlos al final
5. Si aplica a ambas redes, usar `peer*`
