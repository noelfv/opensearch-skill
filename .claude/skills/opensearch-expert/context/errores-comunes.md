# Errores Comunes al Armar Queries

> Registro de errores que el equipo encontró en producción.  
> El agente consulta este archivo para no repetirlos.

---

## [EC-001] Usar `term` en campos de arrays anidados

**Error:**
```json
"term": { "addendumData.additionalData.value": "0110" }
```

**Correcto:**
```json
"match_phrase": { "addendumData.additionalData.value": "0110" }
```

**Por qué:** Los campos dentro de arrays como `additionalData[]` no se indexan igual que campos escalares. `term` no encuentra el valor. Siempre usar `match_phrase` para arrays de objetos con key/value.

---

## [EC-002] Olvidar `.keyword` en aggregations y prefix

**Error:**
```json
"terms": { "field": "processingResult.resultData.otherResult" }
"prefix": { "processingResult.resultData.otherResult": "NO_FOUND" }
```

**Correcto:**
```json
"terms": { "field": "processingResult.resultData.otherResult.keyword" }
"prefix": { "processingResult.resultData.otherResult.keyword": "NO_FOUND" }
```

**Por qué:** Sin `.keyword` el campo está tokenizado (analizado), lo que rompe aggregations exactas y prefix queries.

---

## [EC-003] Omitir `time_zone` en rangos de fecha

**Error:**
```json
"range": {
  "monitoring.countryDate": {
    "gte": "2026-04-13T08:00:00",
    "lte": "2026-04-13T20:00:00"
  }
}
```

**Correcto:**
```json
"range": {
  "monitoring.countryDate": {
    "gte": "2026-04-13T08:00:00",
    "lte": "2026-04-13T20:00:00",
    "time_zone": "-05:00"
  }
}
```

**Por qué:** Sin `time_zone`, OpenSearch interpreta las fechas en UTC. Esto desplaza los resultados 5 horas y retorna datos incorrectos para el negocio.

---

## [EC-004] Usar `size > 0` en queries de solo aggregations

**Error:**
```json
{
  "size": 100,
  "aggs": { ... }
}
```

**Correcto:**
```json
{
  "size": 0,
  "aggs": { ... }
}
```

**Por qué:** Cuando solo interesan las agregaciones, `size: 0` evita traer documentos individuales innecesarios, haciendo la query más rápida.

---

## [EC-006] Usar "Declined" en lugar de "Denied" para transacciones denegadas

**Error:**
```json
"term": { "monitoring.transactionStatus.keyword": "Declined" }
```

**Correcto:**
```json
"term": { "monitoring.transactionStatus.keyword": "Denied" }
```

**Por qué:** El valor real en el índice es `"Denied"`, no `"Declined"`. Usar el valor incorrecto retorna 0 resultados sin error visible.

---

## [EC-005] Buscar `transactionStatus` con `term` en vez de `exists`

**Error:** Buscar transacciones sin respuesta con `term: { "monitoring.transactionStatus": null }` — esto no funciona en OpenSearch.

**Correcto:**
```json
"must_not": [
  { "exists": { "field": "monitoring.transactionStatus" } }
]
```

**Por qué:** Para detectar campos ausentes siempre usar `must_not exists`, nunca comparar con null.

---

## [EC-007] Buscar billeteras PLIN/YAPE por merchantNameAceptor

**Error:**
```json
"match_phrase": { "monitoring.merchantNameAceptor": "PLIN" }
```

**Correcto — por billetera específica:**
```json
"term": { "p2pType.keyword": "PLIN" }
"term": { "p2pType.keyword": "YAPE" }
```

**Correcto — todas las billeteras P2P:**
```json
"term": { "monitoring.channelFilter.keyword": "P2PP" }
```

**Por qué:** Las transacciones de billetera no se identifican por el nombre del comercio sino por el campo `p2pType`. Para obtener todas las billeteras sin importar el tipo, usar `channelFilter: "P2PP"`.

---

<!-- NUEVOS ERRORES AQUÍ — agregar cuando el equipo encuentre uno nuevo -->

## [EC-008] Usar peer01 para BINs de Mastercard (y viceversa)

**Error:** Consultar un BIN de Mastercard en el índice de Visa (o al revés).

**Regla:**
| Red | Índice | Marca | BINs ejemplo |
|-----|--------|-------|--------------|
| PEER01 | `peer01` | Visa | `491914`, `414064`, `477113`... |
| PEER02 | `peer02` | Mastercard | `511578`, `512312`, `553650`... |

**Por qué:** Cada red almacena sus transacciones en su propio índice. Un BIN de Mastercard nunca aparecerá en `peer01`. Siempre verificar el BIN en `doc/application-data.md` antes de elegir el índice.

**Cómo identificar rápido:** BINs que empiezan con `5` generalmente son Mastercard → `peer02`. BINs que empiezan con `4` generalmente son Visa → `peer01`.
