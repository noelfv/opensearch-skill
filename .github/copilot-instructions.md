# Instrucciones del Proyecto — Monitoreo ISO20022

## Contexto obligatorio

Este workspace es un sistema de monitoreo de transacciones financieras. Los mensajes ISO8583 son convertidos a ISO20022 y almacenados en OpenSearch.

## IMPORTANTE — Modo de operación

**NO existe conexión MCP ni acceso directo a OpenSearch.**
**Las queries se generan en DSL y el usuario las copia y pega en OpenSearch Dashboards → Dev Tools.**
No intentar ejecutar queries, no buscar herramientas de conexión a bases de datos, no usar MCP.

## Reglas de negocio

- Zona horaria del negocio: **-05:00** (Lima/Bogotá). Toda query con fechas debe incluirla.
- Campo principal de fecha: `monitoring.countryDate`. Nunca usar `transmissionDateTime`.
- Transacción sin respuesta: ausencia del campo `monitoring.transactionStatus`.
- Índice por defecto: `peer01`. Usar `peer*` solo si se piden ambas redes explícitamente.
- `messageFunction`: "AUTQ"=0100, "FAUQ"=0120, "FRVA"=0400, "RVRA"=0420
- Las queries se entregan listas para copiar/pegar, sin pasos adicionales.

## Redes

| Red | Índice | Marca |
|-----|--------|-------|
| PEER01 | `peer01` | Visa |
| PEER02 | `peer02` | Mastercard |
| Ambas | `peer*` | — |

## Skill activo

Para cualquier consulta relacionada con transacciones, búsquedas, filtros o análisis sobre OpenSearch, usar siempre el skill `opensearch-expert` leyendo el archivo:
`.claude/skills/opensearch-expert/SKILL.md`

El skill incluye el esquema completo del documento, reglas de generación de queries y el contexto acumulado del equipo.
