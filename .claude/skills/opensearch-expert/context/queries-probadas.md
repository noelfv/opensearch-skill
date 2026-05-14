# Queries Probadas en Producción

> Archivo mantenido por el equipo. Agregar queries con `/opensearch-save`.  
> Antes de generar una query nueva, revisar si ya existe una similar aquí.

---

## [QPR-001] Buscar por rango de fechas

**Descripción:** Transacciones en un rango de fechas específico, ordenadas cronológicamente.  
**Cuándo usar:** Soporte operativo, auditoría de un período específico.

```json
POST /peer01/_search
{
  "size": 100,
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "monitoring.countryDate": {
              "gte": "2026-04-13T08:00:00",
              "lte": "2026-04-14T19:05:00",
              "time_zone": "-05:00"
            }
          }
        }
      ]
    }
  },
  "sort": [
    { "monitoring.countryDate": "asc" }
  ]
}
```

**Notas:** Cambiar `gte` y `lte` según el período. Usar `"lte": "now"` para hasta el momento actual.

---

## [QPR-002] Buscar por tipo de mensaje de respuesta (UNSP)

**Descripción:** Filtra transacciones por el tipo de mensaje ISO8583 de respuesta.  
**Cuándo usar:** Identificar reversales, auth responses, financial responses.  
**Valores UNSP:** `0110`=auth resp, `0130`=reversal resp, `0210`=financial resp

```json
POST /peer01/_search
{
  "size": 100,
  "query": {
    "bool": {
      "must": [
        {
          "match_phrase": {
            "addendumData.additionalData.key": "UNSP"
          }
        },
        {
          "match_phrase": {
            "addendumData.additionalData.value": "0130"
          }
        }
      ]
    }
  },
  "sort": [
    { "monitoring.countryDate": "desc" }
  ]
}
```

**Notas:** Usar `match_phrase` y no `term` porque el campo está dentro de un array anidado.

---

## [QPR-003] Operaciones sin respuesta (timeouts)

**Descripción:** Detecta transacciones que no recibieron respuesta del emisor o red.  
**Cuándo usar:** Monitoreo operativo, detección de caídas de servicio, soporte a clientes con transacciones pendientes.

```json
POST /peer01/_search
{
  "size": 100,
  "query": {
    "bool": {
      "must_not": [
        {
          "exists": {
            "field": "monitoring.transactionStatus"
          }
        }
      ]
    }
  },
  "sort": [
    { "monitoring.countryDate": "desc" }
  ]
}
```

**Notas:** La ausencia de `monitoring.transactionStatus` indica que el mensaje de respuesta nunca llegó.

---

## [QPR-004] Agrupar rechazos por código otherResult con prefijo

**Descripción:** Agrupa transacciones cuyo resultado empieza con un prefijo, útil para clasificar tipos de rechazo.  
**Cuándo usar:** Análisis de rechazos, reporte de operaciones NO_FOUND.

```json
POST /peer01/_search
{
  "size": 0,
  "query": {
    "bool": {
      "must": [
        {
          "prefix": {
            "processingResult.resultData.otherResult.keyword": "NO_FOUND"
          }
        },
        {
          "range": {
            "monitoring.countryDate": {
              "gte": "2026-04-23T00:00:00",
              "lte": "now",
              "time_zone": "-05:00"
            }
          }
        }
      ]
    }
  },
  "aggs": {
    "errores_no_found": {
      "terms": {
        "field": "processingResult.resultData.otherResult.keyword",
        "size": 10
      }
    }
  }
}
```

**Notas:** `size: 0` porque solo interesan las agregaciones, no los documentos individuales. El `.keyword` es obligatorio para `prefix` y `terms`.

---

## [QPR-005] Agrupar valores de monitoring.operationFilter

**Descripción:** Agrupa y cuenta todos los tipos de operación distintos en `monitoring.operationFilter` para un período dado.  
**Cuándo usar:** Auditoría de tipos de operación, descubrir o validar valores distintos del campo, análisis de distribución por tipo.

```json
POST /peer01/_search
{
  "size": 0,
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "monitoring.countryDate": {
              "gte": "2026-02-01T00:00:00",
              "lte": "2026-02-28T23:59:59",
              "time_zone": "-05:00"
            }
          }
        }
      ]
    }
  },
  "aggs": {
    "por_operacion": {
      "terms": {
        "field": "monitoring.operationFilter.keyword",
        "size": 50
      }
    }
  }
}
```

**Notas:** `size: 50` cubre todos los valores posibles. Ajustar `gte`/`lte` según el período requerido. Los valores confirmados en producción están en `valores-descubiertos.md`.

---

## [QPR-006] Operaciones de billetera PLIN

**Descripción:** Recupera transacciones de la billetera digital PLIN filtrando por el campo `p2pType`.  
**Cuándo usar:** Soporte operativo de billeteras, monitoreo de PLIN, análisis de transacciones P2P.

```json
POST /peer01/_search
{
  "size": 100,
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "p2pType.keyword": "PLIN"
          }
        },
        {
          "range": {
            "monitoring.countryDate": {
              "gte": "now/d",
              "lte": "now",
              "time_zone": "-05:00"
            }
          }
        }
      ]
    }
  },
  "sort": [
    { "monitoring.countryDate": "desc" }
  ]
}
```

**Notas:** Usar `p2pType.keyword` y NO `monitoring.merchantNameAceptor` para identificar billeteras (EC-007). Para YAPE cambiar el valor a `"YAPE"`. Para todas las billeteras usar `monitoring.channelFilter.keyword: "P2PP"`.

---

<!-- NUEVAS QUERIES AQUÍ — el comando /opensearch-save las agrega automáticamente -->

## [QPR-007] Buscar operaciones por PAN de tarjeta específica

**Descripción:** Filtra todas las transacciones de una tarjeta específica por su PAN enmascarado en un rango de fechas.  
**Cuándo usar:** Soporte operativo, investigación de operaciones de un cliente específico.  
**Red:** PEER02 (Mastercard) — BIN `553650` = Mastercard BLACK.

```json
POST /peer02/_search
{
  "size": 100,
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "environment.card.pan.keyword": "553650******9713"
          }
        },
        {
          "range": {
            "monitoring.countryDate": {
              "gte": "2026-04-11T00:00:00",
              "lte": "2026-04-12T23:59:59",
              "time_zone": "-05:00"
            }
          }
        }
      ]
    }
  },
  "sort": [
    { "monitoring.countryDate": "asc" }
  ]
}
```

**Notas:**
- Campo del PAN: `environment.card.pan.keyword` — siempre con `.keyword` para exact match (EC-002).
- BINs que empiezan con `5` son Mastercard → `peer02`. BINs con `4` son Visa → `peer01` (EC-008).
- Para buscar solo por últimos 4 dígitos usar `wildcard`: `{ "environment.card.pan.keyword": "*9713" }`
- Ajustar `gte`/`lte` según el período requerido.
