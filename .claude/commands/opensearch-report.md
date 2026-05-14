# /opensearch-report

Genera un reporte legible en Markdown a partir del JSON de respuesta de OpenSearch.

## Uso

```
/opensearch-report
```
Luego pega el JSON de respuesta de OpenSearch en el chat.

## Ejemplos de uso

```
/opensearch-report
{ "aggregations": { "por_codigo_respuesta": { ... } } }
```

---

## Instrucciones para Claude

Al recibir este comando con un JSON de respuesta de OpenSearch:

### Paso 1 — Identificar el tipo de respuesta

Detectar qué tipo de agregación contiene el JSON:
- `por_codigo_respuesta` → reporte de códigos de respuesta
- `por_hora` → reporte de volumen por hora
- `por_canal` → reporte por canal
- Si no hay `aggregations`, interpretar los `hits` como lista de transacciones

### Paso 2 — Leer contexto de códigos de respuesta

Si el JSON contiene `por_codigo_respuesta`, leer el archivo:
`doc/codigos-respuesta.md`

Esto permite enriquecer el reporte con la descripción en español de cada código.

### Paso 3 — Calcular totales y métricas

A partir de los buckets calcular:
- **Total de operaciones** = suma de todos los `doc_count`
- **Aprobadas** = bucket con key `"00"` (o key `"APPR"` en nombre)
- **Sin respuesta** = bucket con key `"SIN_RESPUESTA"`
- **Rechazadas** = Total - Aprobadas - Sin respuesta
- **% aprobación** = Aprobadas / Total * 100
- **% rechazo** = Rechazadas / Total * 100

### Paso 4 — Generar el reporte en Markdown

El reporte debe tener esta estructura:

---

#### Estructura del reporte

```
## Reporte de Operaciones — <Red/Índice si se conoce> — <Fecha si se conoce>

### Resumen ejecutivo

| Métrica           | Cantidad | Porcentaje |
|-------------------|----------|------------|
| Total operaciones | X        | 100%       |
| ✅ Aprobadas       | X        | X%         |
| ❌ Rechazadas      | X        | X%         |
| ⏱ Sin respuesta   | X        | X%         |

### Detalle por código de respuesta

#### ✅ Aprobadas
| Código | Nombre técnico | Descripción | Cantidad | % del total |
|--------|---------------|-------------|----------|-------------|
| 00     | APPR          | Aprobada    | X        | X%          |

#### ❌ Rechazadas
| Código | Nombre técnico          | Descripción en español    | Cantidad | % rechazos | % total |
|--------|------------------------|---------------------------|----------|------------|---------|
| 51     | insufficient_debit_...  | Saldo insuficiente        | X        | X%         | X%      |
| 57     | blocked_operation       | Operación bloqueada       | X        | X%         | X%      |
| ...    | ...                     | ...                       | ...      | ...        | ...     |

#### ⏱ Sin respuesta (timeout)
| Categoría      | Cantidad | % del total |
|----------------|----------|-------------|
| SIN_RESPUESTA  | X        | X%          |

### Top 5 motivos de rechazo
1. Código XX — Descripción — X operaciones (X% de rechazos)
2. ...

### Observaciones
- Indicar si hay algún código inusual o con volumen elevado inesperado.
- Mencionar si `SIN_RESPUESTA` supera el 0.1% (puede indicar problema de conectividad).
- Si hay códigos desconocidos, indicarlos explícitamente.
```

### Reglas adicionales

- Siempre redondear porcentajes a 2 decimales.
- Si un bucket tiene múltiples nombres en `nombre_codigo`, listarlos todos separados por ` / `.
- Si `nombre_codigo.buckets` está vacío → mostrar `—` en la columna nombre técnico.
- Usar descripción en español del archivo `doc/codigos-respuesta.md` si existe para ese código y esa red.
- Si no se conoce la red, indicar "Red no especificada".
- Si se conoce la red por contexto del chat, incluirla en el título.
- Al final incluir: `> Reporte generado por GitHub Copilot a partir de respuesta OpenSearch DSL.`
