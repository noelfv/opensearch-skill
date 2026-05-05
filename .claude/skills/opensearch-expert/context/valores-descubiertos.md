# Valores Descubiertos en Producción

> Registrar aquí cualquier valor de campo que no estaba documentado originalmente.  
> Este archivo enriquece al agente para que genere queries más precisas con el tiempo.

---

## Formato de entrada

```
## [VD-XXX] nombre descriptivo
**Campo:** ruta.completa.del.campo
**Valor:** "el valor"
**Significado:** qué representa en el negocio
**Descubierto:** fecha y contexto
```

---

## Valores conocidos al inicio del proyecto

### monitoring.channelFilter
| Valor | Significado |
|-------|-------------|
| `POS` | Punto de venta físico |
| `ATM` | Cajero automático |
| `OTHER` | Otros canales |
| `ECOMMERCE` | Comercio electrónico |

### monitoring.operationFilter
| Valor | Significado |
|-------|-------------|
| `PURCHASE` | Compra |
| `WTHDMON` | Retiro de Cajero (NO usar "WITHDRAWAL") |
| `WTHDMON WINDOW` | Retiro de ventanilla |
| `PAYMENT` | Pagos |
| `MONEY SEND` | Envio de dinero |
| `BALANCE INQUIRY` | Consulta de saldo |
| `REFUND` | Cancelacion de una compra |

### processingResult.resultData.otherResultDetails (códigos ISO8583)
| Valor | Significado |
|-------|-------------|
| `00` | Aprobado |
| `05` | No autorizado |
| `51` | Fondos insuficientes |
| `54` | Tarjeta vencida / expirada |
| `57` | Transacción no permitida |
| `61` | Excede límite de retiro |
| `65` | Excede límite de frecuencia |

### context.transactionContext.additionalData — key: OWNER
| Valor | Significado |
|-------|-------------|
| `ONUS` | Tarjeta del mismo banco (emisor = adquirente) |
| `OFFUS` | Tarjeta de otro banco |

### messageFunction
| Valor | Significado |
|-------|-------------|
| `AUTQ` | auth request |
| `FAUQ` | advice request |
| `FRVA` | reversal request |
| `RVRA` | reversal advice request |


### monitoring.transactionStatus
| Valor | Significado |
|-------|-------------|
| `Approved` | Transacción aprobada |
| `Denied` | Transacción denegada (NO usar "Declined") |

### monitoring.channelFilter — valor para billeteras
| Valor | Significado |
|-------|-------------|
| `P2PP` | Todas las billeteras digitales P2P (PLIN, YAPE, etc.) |

### p2pType — tipo de billetera P2P
| Valor | Significado |
|-------|-------------|
| `PLIN` | Billetera PLIN |
| `YAPE` | Billetera YAPE |

> ⚠️ Para billeteras NO usar `monitoring.merchantNameAceptor`. Usar `p2pType` para una billetera específica o `monitoring.channelFilter: "P2PP"` para todas.

---

<!-- NUEVOS VALORES AQUÍ — agregar manualmente o con /opensearch-save -->
