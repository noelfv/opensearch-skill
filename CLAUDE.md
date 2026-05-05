# Contexto del Proyecto — Monitoreo ISO20022

## ¿Qué es este proyecto?

Sistema de monitoreo de transacciones financieras. Los mensajes ISO8583 que viajan por la red de pagos son convertidos a ISO20022 y almacenados en OpenSearch para análisis y soporte operativo.

## Stack tecnológico

- **Mensajería:** ISO8583 → ISO20022
- **Almacenamiento:** OpenSearch (cloud, sin acceso directo al endpoint)
- **Consultas:** OpenSearch Dashboards → Dev Tools
- **Backend:** Java con Netty y Spring Boot
- **Redes:** peer01 (primaria), peer02 (secundaria)

## Reglas de negocio que nunca cambian

- La zona horaria del negocio es **-05:00** (Lima/Bogotá). Toda query con rango de fechas debe incluirla.
- El campo principal de fecha es `monitoring.countryDate`. Nunca usar `transmissionDateTime` para filtros de negocio.
- Una transacción **sin respuesta** se detecta por la ausencia del campo `monitoring.transactionStatus`.
- `messageFunction:` hace referencia al tipo de mensaje de la operacion "AUTQ"=0100, "FAUQ"=0120 , "FRVA "=0400, "RVRA "=0420 
- `addendumData.additionalData[].key :` hace referencia al tipo de mensaje de la operacion pero de respuesta "0110"=auth response, "0130"=advice response, "0410"=reversal response, "0430"=reversal advice response
- Las queries se ejecutan copiando y pegando en **OpenSearch Dashboards → Dev Tools**.
- Por defecto operar sobre índice `peer01`. Usar `peer*` solo si se pide ambas redes explícitamente.

## Skills disponibles en este proyecto

| Skill | Cuándo usarlo |
|-------|---------------|
| `opensearch-expert` | Cualquier consulta, búsqueda o análisis sobre OpenSearch |

## Comandos disponibles

| Comando | Descripción |
|---------|-------------|
| `/opensearch-query` | Genera una query DSL desde lenguaje natural |
| `/opensearch-save` | Guarda una query validada en el contexto del proyecto |

## Conocimiento acumulado del equipo

El conocimiento que el equipo va descubriendo se guarda en:
```
doc/
├── codigo-respuesta.yml          ← fuente original de códigos de respuesta
└── codigos-respuesta.md          ← versión legible con grupos por categoría

.claude/skills/opensearch-expert/context/
├── queries-probadas.md           ← queries validadas en producción
├── valores-descubiertos.md       ← nuevos campos o valores encontrados
└── errores-comunes.md            ← errores frecuentes al armar queries
```

Antes de generar cualquier query:
1. Revisar `context/queries-probadas.md` por si ya existe una similar
2. Consultar `doc/codigos-respuesta.md` si la query involucra resultados o rechazos
3. Evitar los patrones de `context/errores-comunes.md`

## Redes y sus índices

| Red | Índice | Marca |
|-----|--------|-------|
| PEER01 | `peer01` | Visa |
| PEER02 | `peer02` | Mastercard |
| Ambas | `peer*` | — |
