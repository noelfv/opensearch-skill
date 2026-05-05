# Códigos de Respuesta por Red

> **Fuente:** `codigo-respuesta.yml`  
> **Relacionado:** [[SKILL]] | [[valores-descubiertos]] | [[errores-comunes]]  
> **Campos OpenSearch:**
> - `processingResult.resultData.otherResult` → nombre del código (ej: `insufficient_credit_balance`)
> - `processingResult.resultData.otherResultDetails` → valor ISO8583 (ej: `51`)
>
> Ambos campos representan lo mismo. Se puede buscar por cualquiera de los dos.

---

## Cómo buscar por código de respuesta

```
# Por nombre (otherResult) → usar .keyword
"term": { "processingResult.resultData.otherResult.keyword": "insufficient_credit_balance" }

# Por valor ISO8583 (otherResultDetails) → usar .keyword
"term": { "processingResult.resultData.otherResultDetails.keyword": "51" }

# Múltiples nombres del mismo código → usar terms
"terms": { "processingResult.resultData.otherResult.keyword": [
  "insufficient_credit_balance",
  "not_enough_balance",
  "insufficient_debit_balance",
  "exceeded_limits",
  "exceeded_credit_limit"
]}
```

> ⚠️ **Importante:** Un mismo `otherResultDetails` puede tener varios `otherResult`. Por ejemplo el código `51` agrupa 5 nombres distintos. Al buscar por valor ISO8583 obtienes todos; al buscar por nombre obtienes solo ese subtipo.

---

## PEER01 — Red Visa

| Código ISO8583 | Nombre (otherResult) | Descripción de negocio |
|---------------|----------------------|------------------------|
| `00` | `APPR` | Aprobado |
| `01` | `transaction_error_pending` | Error de transacción pendiente |
| `02` | `invalid_date_time` | Fecha/hora inválida |
| `02` | `data_presence_error` | Error de presencia de datos |
| `02` | `invalid_mac` | MAC inválido |
| `02` | `required_input_field` | Campo de entrada requerido |
| `02` | `required_field` | Campo requerido |
| `03` | `merchant_not_allowed` | Comercio no permitido |
| `04` | `legal_review_retain` | Retener — revisión legal |
| `05` | `arqc_error` | Error ARQC (chip) |
| `05` | `legal_agreement_retain` | Retener — acuerdo legal |
| `05` | `merchant_invalid_data` | Datos de comercio inválidos |
| `05` | `limit_denials_exceeded` | Límite de rechazos excedido |
| `05` | `card_legal_agreement` | Tarjeta con acuerdo legal |
| `06` | `account_logic_block` | Bloqueo lógico de cuenta |
| `06` | `incorrect_amount` | Monto incorrecto |
| `06` | `operation_restrictions` | Restricciones de operación |
| `06` | `account_selection_error` | Error de selección de cuenta |
| `06` | `card_deactivated` | Tarjeta desactivada |
| `06` | `card_offus_direct_debit` | Débito directo OFFUS |
| `06` | `transaction_error` | Error de transacción |
| `06` | `installments_not_allowed` | Cuotas no permitidas |
| `06` | `restricted_operation` | Operación restringida |
| `06` | `no_transaction_found` | Transacción no encontrada |
| `07` | `card_fraud` | Fraude de tarjeta |
| `07` | `fraud_retain` | Retener — fraude |
| `10` | `partial_approval` | Aprobación parcial |
| `11` | `approval_vip` | Aprobación VIP |
| `12` | `transaction_error_pending` | Error de transacción pendiente |
| `13` | `invalid_balance` | Saldo inválido |
| `14` | `unreachable_account` | Cuenta no alcanzable |
| `14` | `canceled_card` | Tarjeta cancelada |
| `14` | `card_decline_all` | Tarjeta rechaza todo |
| `14` | `card_not_found` | Tarjeta no encontrada |
| `14` | `cvv1_error` | Error CVV1 |
| `14` | `card_delinquent` | Tarjeta con deuda |
| `14` | `non_operational_card` | Tarjeta no operativa |
| `14` | `pan_contains_non_digit` | PAN con caracteres no numéricos |
| `14` | `card` | Error de tarjeta |
| `14` | `card_vault` | Error en bóveda de tarjeta |
| `14` | `card_not_active` | Tarjeta no activa |
| `14` | `canceled_card_bank` | Tarjeta cancelada por banco |
| `14` | `canceled_card_client` | Tarjeta cancelada por cliente |
| `14` | `card_blocked` | Tarjeta bloqueada |
| `14` | `card_disable` | Tarjeta deshabilitada |
| `19` | `unreachable_currency` | Moneda no alcanzable |
| `19` | `currency_not_supported` | Moneda no soportada |
| `19` | `zero_amount_acct_oper` | Operación de cuenta con monto cero |
| `19` | `prepaid_bin_not_allowed` | BIN prepago no permitido |
| `19` | `incorrect_installment_number` | Número de cuotas incorrecto |
| `19` | `addl_business_card_not_allowed` | Tarjeta empresarial adicional no permitida |
| `19` | `account_type_not_allowed` | Tipo de cuenta no permitido |
| `19` | `associated_account_no_access` | Sin acceso a cuenta asociada |
| `19` | `sticker_contactless_only` | Solo sticker contactless |
| `19` | `service_not_allowed` | Servicio no permitido |
| `19` | `fake_card` | Tarjeta falsa |
| `19` | `ecom_not_allowed` | Ecommerce no permitido |
| `19` | `restriction_not_allowed` | Restricción no permitida |
| `19` | `restricted_operation_channel` | Canal de operación restringido |
| `19` | `incorrect_message_format` | Formato de mensaje incorrecto |
| `19` | `client_denial` | Rechazo del cliente |
| `19` | `card_blocked_bank` | Tarjeta bloqueada por banco |
| `19` | `not_allowed_operation` | Operación no permitida |
| `19` | `invalid_address` | Dirección inválida |
| `19` | `debit_card_restricted` | Tarjeta débito restringida |
| `19` | `invalid_field` | Campo inválido |
| `19` | `message_function` | Error en función de mensaje |
| `25` | `no_results_available` | Sin resultados disponibles |
| `25` | `unreachable_reference` | Referencia no alcanzable |
| `32` | `account_selection_ok` | Selección de cuenta OK |
| `41` | `lost_card_retain` | Retener — tarjeta perdida |
| `43` | `stolen_card_retain` | Retener — tarjeta robada |
| `46` | `contract_end` | Fin de contrato |
| `46` | `cancelled_account` | Cuenta cancelada |
| **`51`** | **`insufficient_credit_balance`** | **Saldo crédito insuficiente** |
| **`51`** | **`exceeded_limits`** | **Límites excedidos** |
| **`51`** | **`not_enough_balance`** | **Saldo insuficiente** |
| **`51`** | **`exceeded_credit_limit`** | **Límite de crédito excedido** |
| **`51`** | **`insufficient_debit_balance`** | **Saldo débito insuficiente** |
| `52` | `non_existent_account` | Cuenta no existente |
| `52` | `cancelled_account` | Cuenta cancelada |
| `53` | `missing_savings_account` | Cuenta de ahorros faltante |
| `54` | `expiration_date_not_valid` | Fecha de expiración no válida |
| `54` | `expired_card` | Tarjeta expirada |
| `54` | `expiration_date` | Error en fecha de expiración |
| `54` | `card_renewal_error` | Error de renovación de tarjeta |
| `54` | `card_expiration_date_error` | Error en fecha de vencimiento |
| `55` | `pin_error` | Error de PIN |
| `57` | `card_denied_court_order` | Tarjeta bloqueada por orden judicial |
| `57` | `bin_retry_parameter` | Parámetro de reintento BIN |
| `57` | `bin_parameters_error` | Error de parámetros BIN |
| `57` | `bin_id_error` | Error de ID de BIN |
| `57` | `bin_type_error` | Error de tipo de BIN |
| `58` | `fallback_blocked` | Fallback bloqueado |
| `59` | `suspect_fraud` | Fraude sospechoso |
| `59` | `prev_block_court_order` | Bloqueo previo por orden judicial |
| `61` | `exceeded_daily_card_limit` | Límite diario de tarjeta excedido |
| `62` | `prev_block_payment_due_denial` | Bloqueo por pago vencido |
| `62` | `blocked_account` | Cuenta bloqueada |
| `62` | `closed_account` | Cuenta cerrada |
| `62` | `account_blocked` | Cuenta bloqueada |
| `75` | `exceeded_pin_retry` | Reintentos de PIN excedidos |
| `78` | `card_not_delivered` | Tarjeta no entregada |
| `79` | `annuled_authorization` | Autorización anulada |
| `82` | `cvv2_autentication_error` | Error de autenticación CVV2 |
| `82` | `cvv2_not_printed` | CVV2 no impreso |
| `82` | `cvv2_not_present` | CVV2 no presente |
| `82` | `cvv2_not_received` | CVV2 no recibido |
| `82` | `cvv2_invalid` | CVV2 inválido |
| `82` | `cavv_not_valid` | CAVV no válido |
| `82` | `operation_denied` | Operación denegada |
| `85` | `original_transaction_denied` | Transacción original rechazada |
| `91` | `issuer_not_available` | Emisor no disponible |
| `91` | `non_existent_merchant` | Comercio no existente |
| `91` | `exceeded_credit_limit` | Límite de crédito excedido |
| `91` | `deadline_exceeded` | Tiempo límite excedido |
| `91` | `card_denied_bank` | Tarjeta rechazada por banco |
| `93` | `cash_withdrawal_blocked` | Retiro de efectivo bloqueado |
| `93` | `internet_purchase_blocked` | Compra por internet bloqueada |
| `93` | `international_purchase_blocked` | Compra internacional bloqueada |
| `96` | `system_error` | Error de sistema |
| `96` | `generic_error` | Error genérico |
| `96` | `internal_failure` | Falla interna |
| `96` | `chameleon_connection_fail` | Falla de conexión Chameleon |
| `96` | `titan_connection_fail` | Falla de conexión Titan |
| `N7` | `transaction_error_pending` | Error de transacción pendiente |
| `5C` | `transaction_error_pending` | Error de transacción pendiente |
| `R3` | `transaction_error_pending` | Error de transacción pendiente |
| `9G` | `transaction_error_pending` | Error de transacción pendiente |

---

## PEER02 — Red Mastercard

| Código ISO8583 | Nombre (otherResult) | Descripción de negocio |
|---------------|----------------------|------------------------|
| `00` | `APPR` | Aprobado |
| `01` | `transaction_error_pending` | Error de transacción pendiente |
| `03` | `non_existent_merchant` | Comercio no existente |
| `04` | `contract_end` | Fin de contrato |
| `04` | `canceled_card_bank` | Tarjeta cancelada por banco |
| `04` | `canceled_card_client` | Tarjeta cancelada por cliente |
| `05` | `limit_denials_exceeded` | Límite de rechazos excedido |
| `05` | `merchant_invalid_data` | Datos de comercio inválidos |
| `05` | `card_legal_agreement` | Tarjeta con acuerdo legal |
| `05` | `legal_agreement_retain` | Retener — acuerdo legal |
| `06` | `no_transaction_found` | Transacción no encontrada |
| `10` | `partial_approval` | Aprobación parcial |
| `12` | `original_transaction_denied` | Transacción original rechazada |
| `13` | `invalid_balance` | Saldo inválido |
| `13` | `incorrect_amount` | Monto incorrecto |
| `13` | `zero_amount_acct_oper` | Operación con monto cero |
| `14` | `card_not_found` | Tarjeta no encontrada |
| `14` | `card_not_active` | Tarjeta no activa |
| `14` | `cvc1_error` | Error CVC1 |
| `14` | `card_disable` | Tarjeta deshabilitada |
| `14` | `non_operational_card` | Tarjeta no operativa |
| `25` | `unreachable_reference` | Referencia no alcanzable |
| `25` | `no_results_available` | Sin resultados disponibles |
| `30` | `invalid_date_time` | Fecha/hora inválida |
| `30` | `incorrect_message_format` | Formato de mensaje incorrecto |
| `30` | `data_presence_error` | Error de presencia de datos |
| `30` | `required_input_field` | Campo de entrada requerido |
| `30` | `invalid_field` | Campo inválido |
| `30` | `required_field` | Campo requerido |
| `30` | `message_function` | Error en función de mensaje |
| `32` | `account_selection_ok` | Selección de cuenta OK |
| `34` | `reach_issuer_susfraud` | Fraude sospechoso — contactar emisor |
| `41` | `lost_card_retain` | Retener — tarjeta perdida |
| `43` | `stolen_card_retain` | Retener — tarjeta robada |
| **`51`** | **`insufficient_credit_balance`** | **Saldo crédito insuficiente** |
| **`51`** | **`exceeded_limits`** | **Límites excedidos** |
| **`51`** | **`not_enough_balance`** | **Saldo insuficiente** |
| **`51`** | **`exceeded_credit_limit`** | **Límite de crédito excedido** |
| **`51`** | **`insufficient_debit_balance`** | **Saldo débito insuficiente** |
| `54` | `invalid_expiration_date` | Fecha de expiración inválida |
| `54` | `card_deactivated` | Tarjeta desactivada |
| `54` | `card_renewal_error` | Error de renovación |
| `54` | `expired_card` | Tarjeta expirada |
| `55` | `pin_error` | Error de PIN |
| `57` | `bin_retry_parameter` | Parámetro de reintento BIN |
| `57` | `user_not_authorized` | Usuario no autorizado |
| `57` | `not_authorized` | No autorizado |
| `57` | `blocked_account` | Cuenta bloqueada |
| `57` | `card_not_delivered` | Tarjeta no entregada |
| `57` | `ecom_not_allowed` | Ecommerce no permitido |
| `57` | `cash_withdrawal_blocked` | Retiro de efectivo bloqueado |
| `57` | `internet_purchase_blocked` | Compra por internet bloqueada |
| `57` | `international_purchase_blocked` | Compra internacional bloqueada |
| `57` | `fallback_blocked` | Fallback bloqueado |
| `57` | `currency_not_supported` | Moneda no soportada |
| `57` | `operation_denied` | Operación denegada |
| `58` | `sticker_contactless_only` | Solo sticker contactless |
| `61` | `exceeded_daily_card_limit` | Límite diario excedido |
| `61` | `exceeded_limit` | Límite excedido |
| `61` | `card_denied_bank` | Tarjeta rechazada por banco |
| `61` | `debit_card_restricted` | Tarjeta débito restringida |
| `62` | `pan_contains_non_digit` | PAN con caracteres no numéricos |
| `62` | `card_blocked_bank` | Tarjeta bloqueada por banco |
| `62` | `canceled_card` | Tarjeta cancelada |
| `62` | `prev_block_payment_due_denial` | Bloqueo por pago vencido |
| `62` | `card_blocked` | Tarjeta bloqueada |
| `62` | `client_denial` | Rechazo del cliente |
| `62` | `card_decline_all` | Tarjeta rechaza todo |
| `62` | `card_delinquent` | Tarjeta con deuda |
| `63` | `invalid_mac` | MAC inválido |
| `63` | `suspect_fraud` | Fraude sospechoso |
| `65` | `exceeded_limit_withdrawal` | Límite de retiro excedido |
| `75` | `exceeded_pin_retry` | Reintentos de PIN excedidos |
| `76` | `account_selection_error` | Error de selección de cuenta |
| `76` | `closed_account` | Cuenta cerrada |
| `76` | `non_existent_account` | Cuenta no existente |
| `77` | `unreachable_account` | Cuenta no alcanzable |
| `88` | `chip_arqc_error` | Error ARQC de chip |
| `88` | `dcvv2_expired` | DCVV2 expirado |
| `88` | `dcvv2_not_match` | DCVV2 no coincide |
| `88` | `cavv_not_valid` | CAVV no válido |
| `88` | `fake_card` | Tarjeta falsa |
| `91` | `issuer_not_available` | Emisor no disponible |
| `91` | `deadline_exceeded` | Tiempo límite excedido |
| `94` | `annuled_authorization` | Autorización anulada |
| `96` | `system_error` | Error de sistema |
| `96` | `transaction_error` | Error de transacción |
| `96` | `internal_failure` | Falla interna |
| `96` | `generic_error` | Error genérico |
| `96` | `chameleon_connection_fail` | Falla de conexión Chameleon |
| `96` | `titan_connection_fail` | Falla de conexión Titan |

---

## Grupos de códigos por categoría de negocio

Útil para construir queries con `terms` que cubran todos los subtipos de un problema.

### Saldo insuficiente (código 51)
```json
"terms": { "processingResult.resultData.otherResult.keyword": [
  "insufficient_credit_balance",
  "insufficient_debit_balance",
  "not_enough_balance",
  "exceeded_limits",
  "exceeded_credit_limit"
]}
```

### Tarjeta con problemas de vigencia (código 54)
```json
"terms": { "processingResult.resultData.otherResult.keyword": [
  "expired_card",
  "expiration_date_not_valid",
  "expiration_date",
  "card_renewal_error",
  "card_expiration_date_error",
  "invalid_expiration_date",
  "card_deactivated"
]}
```

### Tarjeta bloqueada / cancelada
```json
"terms": { "processingResult.resultData.otherResult.keyword": [
  "card_blocked",
  "card_blocked_bank",
  "canceled_card",
  "canceled_card_bank",
  "canceled_card_client",
  "card_disable",
  "card_not_active"
]}
```

### Fraude
```json
"terms": { "processingResult.resultData.otherResult.keyword": [
  "card_fraud",
  "fraud_retain",
  "suspect_fraud",
  "fake_card",
  "reach_issuer_susfraud"
]}
```

### Error de CVV/CVC
```json
"terms": { "processingResult.resultData.otherResult.keyword": [
  "cvv2_autentication_error",
  "cvv2_not_printed",
  "cvv2_not_present",
  "cvv2_not_received",
  "cvv2_invalid",
  "cvv2_not_validated",
  "cavv_not_valid",
  "cvc1_error",
  "cvv1_error",
  "dcvv2_expired",
  "dcvv2_not_match"
]}
```

### Emisor no disponible / timeout de red
```json
"terms": { "processingResult.resultData.otherResult.keyword": [
  "issuer_not_available",
  "deadline_exceeded",
  "chameleon_connection_fail",
  "chameleon_null_response",
  "titan_connection_fail"
]}
```

### Error interno del sistema
```json
"terms": { "processingResult.resultData.otherResult.keyword": [
  "system_error",
  "generic_error",
  "internal_failure",
  "transaction_error",
  "chameleon_bad_response_format",
  "chameleon_bad_response_body",
  "titan_transaction_error"
]}
```

### Límites excedidos (diario, retiro, frecuencia)
```json
"terms": { "processingResult.resultData.otherResult.keyword": [
  "exceeded_daily_card_limit",
  "exceeded_limit_withdrawal",
  "exceeded_limit",
  "exceeded_pin_retry",
  "limit_denials_exceeded"
]}
```

### Operaciones bloqueadas por restricción
```json
"terms": { "processingResult.resultData.otherResult.keyword": [
  "cash_withdrawal_blocked",
  "internet_purchase_blocked",
  "international_purchase_blocked",
  "ecom_not_allowed",
  "fallback_blocked",
  "restricted_operation",
  "restricted_operation_channel",
  "not_allowed_operation"
]}
```
