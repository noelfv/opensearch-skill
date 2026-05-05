# /opensearch-save

Guarda una query validada en producción dentro del contexto del proyecto para que el equipo y el agente la usen en el futuro.

## Uso

```
/opensearch-save "<nombre descriptivo>"
```

Ejecutar justo después de que `/opensearch-query` generó una query que funcionó correctamente.

## Ejemplos

```
/opensearch-save "reversales ONUS de las últimas 24 horas"
/opensearch-save "sin respuesta agrupados por hora"
/opensearch-save "rechazos por fondos insuficientes esta semana"
```

---

## Instrucciones para Claude

Al recibir este comando:

### Paso 1 — Recuperar la query de la conversación
Tomar la última query JSON generada en esta conversación (la que el usuario acaba de validar).

### Paso 2 — Leer el archivo de queries probadas
Leer `.claude/skills/opensearch-expert/context/queries-probadas.md` para:
- Determinar el siguiente número de QPR (QPR-005, QPR-006, etc.)
- Verificar que no existe ya una query idéntica

### Paso 3 — Agregar la nueva entrada al archivo
Editar `.claude/skills/opensearch-expert/context/queries-probadas.md` agregando **antes** del comentario final `<!-- NUEVAS QUERIES AQUÍ -->` la siguiente estructura:

```markdown
## [QPR-XXX] $ARGUMENTS

**Descripción:** [descripción de lo que hace la query]  
**Cuándo usar:** [casos de uso típicos]

\`\`\`json
[la query completa aquí]
\`\`\`

**Notas:** [observaciones importantes, campos clave, variaciones posibles]

---
```

### Paso 4 — Verificar si hay valores nuevos que documentar
Si la query usa algún valor de campo que no está en `context/valores-descubiertos.md`, agregarlo también a ese archivo con el formato correspondiente.

### Paso 5 — Confirmar al usuario
Responder con:
```
✅ Query guardada como [QPR-XXX] en queries-probadas.md
📁 Disponible para el equipo y sesiones futuras de Claude Code
```

$ARGUMENTS
