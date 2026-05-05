# /opensearch-query

Genera una query OpenSearch DSL desde lenguaje natural, consultando primero el conocimiento acumulado del equipo.

## Uso

```
/opensearch-query <descripción en lenguaje natural>
```

## Ejemplos

```
/opensearch-query transacciones aprobadas de hoy en POS
/opensearch-query sin respuesta de las últimas 2 horas
/opensearch-query rechazos agrupados por código esta semana
/opensearch-query busca el RRN 000011361465
/opensearch-query reversales ONUS de ayer
/opensearch-query cuántas transacciones por hora hubo ayer
/opensearch-query internacionales declinadas por fondos insuficientes este mes
```

---

## Instrucciones para Claude

Al recibir este comando seguir este flujo **en orden**:

### Paso 1 — Leer el contexto acumulado
Leer los tres archivos de contexto antes de generar nada:
- `.claude/skills/opensearch-expert/context/queries-probadas.md`
- `.claude/skills/opensearch-expert/context/valores-descubiertos.md`
- `.claude/skills/opensearch-expert/context/errores-comunes.md`

### Paso 2 — Verificar si ya existe una query similar
Si `queries-probadas.md` tiene una query que resuelve el mismo problema, adaptarla en lugar de crear desde cero. Indicar en la respuesta de cuál QPR se partió.

### Paso 3 — Identificar la intención
- Búsqueda simple → `query.bool.must` con filtros
- Sin respuesta / timeout → `must_not exists monitoring.transactionStatus`
- Solo aggregaciones → `size: 0` + `aggs`
- Búsqueda por ID → `match_phrase` sobre el campo exacto
- Combinada → filtros + aggregaciones juntos

### Paso 4 — Construir la query evitando errores conocidos
Aplicar las reglas del skill y evitar los errores de `errores-comunes.md`:
- Arrays anidados → `match_phrase`, no `term`
- Aggregations / prefix → siempre `.keyword`
- Fechas → siempre `time_zone: "-05:00"`
- Solo aggregations → `size: 0`
- Campo ausente → `must_not exists`, no comparar con null

### Paso 5 — Responder con este formato
```
📋 **Basado en:** [QPR-XXX si aplica, o "query nueva"]

[bloque JSON de la query]

**Qué hace:** explicación en 2-3 líneas
**Supuestos:** [si asumiste fechas u otros valores, mencionarlos]
**Sugerencia:** si la query es útil, guardarla con /opensearch-save "[nombre descriptivo]"
```

$ARGUMENTS
