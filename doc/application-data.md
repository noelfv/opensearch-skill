# Datos de Aplicación — Referencia de Campos

> **Fuente:** `application-data.yml`  
> **Relacionado:** [[SKILL]] | [[valores-descubiertos]] | [[codigos-respuesta]]  
> **Campos OpenSearch relacionados:**
> - `monitoring.binCode` → código BIN de la tarjeta
> - `monitoring.binDescription` → descripción del producto (ej: `PLATINUM`)
> - `context.transactionContext.merchantCategoryCode` → MCC del comercio
> - `transaction.transactionAmounts.transactionAmount.currency` → código de moneda ISO
> - `environment.acquirer.id` o `environment.sender.id` → ID de entidad P2P (para billeteras)

---

## Monedas

| Código Numérico | Código ISO | Decimales |
|----------------|-----------|-----------|
| `604` | `PEN` | 2 |
| `840` | `USD` | 2 |
| `978` | `EUR` | 2 |
| `986` | `BRL` | 2 |
| `170` | `COP` | 2 |
| `152` | `CLP` | 2 |
| `032` | `ARS` | 2 |

**Cómo buscar por moneda:**
```json
"term": { "transaction.transactionAmounts.transactionAmount.currency.keyword": "PEN" }
```

---

## BINs por Red

### PEER01 — Visa

| BIN | Producto |
|-----|---------|
| `428597` | DEBITO EMPRESARIAL |
| `438168` | VIP ELECTRON |
| `455103` | COMPRAS |
| `455170` | MUNDO SUELDO |
| `477113` | PREPAGO DISEÑO |
| `404293` | CUOTA CERO |
| `414064` | LIFEMILES ORO |
| `414068` | LIFEMILES PLATINUM |
| `414075` | INFINITE |
| `414089` | SIGNATURE LIFEMILES |
| `414791` | SIGNATURE |
| `491909` | BFREE |
| `491910` | CLASICA REPSOL |
| `491911` | ORO |
| `491914` | PLATINUM |
| `491931` | CUOTA FIJA |
| `491913` | EMPRESARIAL ORO |
| `491912` | CAPITAL DE TRABAJO |
| `491947` | CORPORATE |
| `491946` | CUENTA VIAJES |

### PEER02 — Mastercard

| BIN | Producto |
|-----|---------|
| `519348` | TARJETA HINCHA |
| `511578` | PLATINUM |
| `512312` | CLASICA |
| `512409` | ORO |
| `553650` | BLACK |

**Cómo buscar por BIN:**
```json
"term": { "monitoring.binCode.keyword": "491914" }
```

**Cómo buscar por descripción de producto:**
```json
"term": { "monitoring.binDescription.keyword": "PLATINUM" }
```

---

## Entidades P2P (Billeteras)

> Usadas en operaciones PLIN/YAPE. El ID de entidad se encuentra en `environment.acquirer.id` o `environment.sender.id`.

| ID Entidad | Banco / Entidad |
|-----------|----------------|
| `650036704` | BBVA |
| `650036525` | INTERBANK |
| `650039140` | SCOTIABANK |
| `650161120` | BANBIF |
| `100047268` | BCP |
| `650181632` | CAJA AREQUIPA |
| `650192246` | CAJA SULLANA |
| `650197514` | CAJA ICA |
| `650184942` | CAJA PIURA |
| `650184943` | CAJA TACNA |
| `650187926` | CAJA SULLANA YAPE |
| `650189314` | CAJA TRUJILLO YAPE |
| `650184944` | BANCO NACION |
| `650180877` | YAPE CARD |
| `650212662` | MI BANCO |
| `650196944` | CAJA HUANCAYO |
| `650241569` | ENTIDAD_NO_FOUND |
| `650240708` | ENTIDAD_NO_FOUND |
| `650218369` | ENTIDAD_NO_FOUND |
| `650238611` | ENTIDAD_NO_FOUND |
| `650190321` | ENTIDAD_NO_FOUND |
| `650195861` | ENTIDAD_NO_FOUND |
| `651008214` | ENTIDAD_NO_FOUND |
| `650173712` | ENTIDAD_NO_FOUND |
| `650246823` | ENTIDAD_NO_FOUND |

**Cómo buscar transacciones de BCP:**
```json
"term": { "environment.acquirer.id.keyword": "100047268" }
```

> ⚠️ Los registros con `ENTIDAD_NO_FOUND` son entidades aún no mapeadas en el sistema.

---

## Categorías de Comercio (MCC)

> Campo OpenSearch: `context.transactionContext.merchantCategoryCode`

| MCC | Descripción |
|-----|------------|
| `6012` | INSTITUCIONES FINANCIERAS — MERCADERÍA |
| `4829` | TRANSFERENCIAS DE DINERO |
| `6011` | INSTITUCIONES FINANCIERAS — DESEMBOLSOS AUTOMÁTICOS |
| `5411` | TIENDAS DE COMESTIBLES Y SUPERMERCADOS |
| `7372` | PROGRAMACIÓN DE COMPUTADORAS Y PROCESAMIENTO |
| `5812` | RESTAURANTES |
| `5541` | ESTACIONES DE SERVICIO Y GASOLINERAS |
| `5499` | TIENDAS DE PRODUCTOS ALIMENTICIOS DIVERSOS |
| `5300` | TIENDAS MAYORISTAS DE MERCANCÍA GENERAL |
| `5462` | PANADERÍAS Y PASTELERÍAS |
| `5814` | RESTAURANTES DE COMIDA RÁPIDA |
| `5912` | BOTICAS Y FARMACIAS |
| `5818` | VENTAS VIRTUALES — PRODUCTOS DIGITALES |
| `4784` | AUTOPISTAS, PUENTES Y TÚNELES DE PEAJE |
| `7995` | APUESTAS |
| `4899` | CABLE, SATÉLITE, TV Y RADIO |
| `4121` | SERVICIO DE TAXIS Y LIMUSINAS |
| `6300` | SEGUROS |
| `5311` | TIENDAS POR DEPARTAMENTOS |
| `5816` | VENTAS VIRTUALES — JUEGOS DIGITALES |
| `8062` | HOSPITALES |
| `7523` | ESTACIONAMIENTOS Y GARAJES |
| `7399` | SERVICIOS COMERCIALES |
| `5200` | TIENDAS DE PRODUCTOS PARA EL HOGAR |
| `9311` | PAGOS DE IMPUESTOS |
| `5817` | VENTAS VIRTUALES DE APLICACIONES |
| `5813` | BARES, CLUBES NOCTURNOS Y DISCOTECAS |
| `7311` | SERVICIOS DE PUBLICIDAD |
| `7011` | HOSPEDAJE Y HOTELES |
| `5734` | TIENDAS DE COMPUTADORAS Y SOFTWARE |
| `4789` | SERVICIOS DE TRANSPORTE |
| `7832` | CINES |
| `4900` | SERVICIOS PÚBLICOS — GAS, ELECTRICIDAD |
| `4814` | SERVICIOS DE TELECOMUNICACIONES |
| `8099` | SERVICIOS MÉDICOS Y SALUD |
| `8220` | UNIVERSIDADES E INSTITUTOS SUPERIORES |
| `4111` | TRANSPORTE LOCAL Y SUBURBANO |
| `4511` | AEROLÍNEAS Y TRANSPORTE AÉREO |
| `4722` | AGENCIAS DE VIAJE |
| `5661` | TIENDAS DE CALZADO |
| `5311` | TIENDAS POR DEPARTAMENTOS |
| `8011` | DOCTORES Y MÉDICOS |
| `8021` | DENTISTAS Y ORTODONCISTAS |
| `5732` | TIENDAS DE EQUIPOS ELECTRÓNICOS |
| `5722` | TIENDAS DE ELECTRODOMÉSTICOS |
| `7512` | AGENCIAS DE ALQUILER DE AUTOMÓVILES |
| `5533` | TIENDAS DE AUTOPARTES Y ACCESORIOS |
| `5511` | CONCESIONARIOS DE AUTOMÓVILES |
| `7538` | TALLERES DE REPARACIÓN AUTOMOTRIZ |
| `6051` | INSTITUCIONES NO FINANCIERAS — CHEQUES DE VIAJERO |
| `9399` | SERVICIOS GUBERNAMENTALES |
| `5999` | TIENDAS MINORISTAS ESPECIALIZADAS |
| `5251` | FERRETERÍAS |
| `5211` | TIENDAS DE MADERA Y MATERIALES DE CONSTRUCCIÓN |
| `1520` | CONSTRUCCIÓN Y REMODELACIÓN |
| `4131` | LÍNEAS DE AUTOBUSES |
| `4112` | TRENES DE PASAJEROS |
| `4411` | NAVÍOS Y EMPRESAS DE CRUCERO |
| `9222` | MULTAS |
| `9211` | COSTAS JUDICIALES |
| `9402` | EMPRESAS DE SERVICIO POSTAL |
| `6513` | ALQUILER DE BIENES INMOBILIARIOS |
| `6211` | CORREDORES DE TÍTULOS VALORES |
| `6540` | INSTITUCIONES NO FINANCIERAS — COMPRA |
| `6010` | INSTITUCIONES FINANCIERAS — DESEMBOLSOS MANUALES |
| `7997` | CLUBES DEPORTIVOS Y CAMPESTRES |
| `7941` | CLUBES Y EVENTOS DEPORTIVOS |
| `5941` | TIENDAS DE ARTÍCULOS DEPORTIVOS |
| `5655` | TIENDAS DE PRENDAS DEPORTIVAS |
| `7922` | PRODUCTORES TEATRALES Y VENTA DE ENTRADAS |
| `7829` | PRODUCCIÓN Y DISTRIBUCIÓN DE PELÍCULAS |
| `7991` | ATRACCIONES Y EXHIBICIONES TURÍSTICAS |
| `7993` | MÁQUINAS TRAGAMONEDAS |
| `7994` | SALAS DE VIDEOJUEGOS |
| `7996` | PARQUES DE DIVERSIONES Y CIRCOS |
| `7998` | ZOOLÓGICOS Y ACUARIOS |
| `7999` | SERVICIOS DE RECREACIÓN |
| `0742` | SERVICIOS VETERINARIOS |
| `0780` | SERVICIOS DE JARDINERÍA |
| `0763` | COOPERATIVAS AGRÍCOLAS |

**Cómo buscar por MCC:**
```json
"term": { "context.transactionContext.merchantCategoryCode.keyword": "5411" }
```

**Cómo buscar múltiples MCC (ej: restaurantes):**
```json
"terms": { "context.transactionContext.merchantCategoryCode.keyword": ["5812", "5814", "5813"] }
```
