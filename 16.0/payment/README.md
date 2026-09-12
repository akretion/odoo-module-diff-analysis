# payment migration guide (15.0 -> 16.0)

## What's new for users

- **Acquirers are now called Providers.** The wording changed everywhere: menus, configuration screens, transactions and saved payment methods. This is a vocabulary change, not a functional one.
- **Filter providers by amount.** A new *Maximum Amount* field on a provider hides it from the payment form when the order total exceeds that amount (leave empty for no limit).
- **Publish / unpublish.** A provider can be hidden from customers without being disabled.
- **Tokens of disabled providers are hidden** from the customer portal.
- **Payment token display** was reworked to fit any screen size.
- **Demo provider is complete.** It supports every feature (tokenization, manual capture, fees, refunds) and lets you pick the payment outcome to test each flow.
- **No Invoicing/Accounting required.** Online payment providers can now be used without installing the Invoicing or Accounting apps; invoice and payment post-processing is now provided by those apps when they are installed.

## Technical data model changes

**Renaming (largest impact).** Model `payment.acquirer` becomes `payment.provider`; the selection field `provider` becomes `code`; related fields become `provider_code` or `provider_id`. On `payment.transaction`: `acquirer_id` -> `provider_id`, `acquirer_reference` -> `provider_reference`. On `payment.token`: `acquirer_id` -> `provider_id`, `acquirer_ref` -> `provider_ref`. On `payment.icon`: `acquirer_ids` -> `provider_ids`. On `res.company`: `payment_acquirer_onboarding_state` -> `payment_provider_onboarding_state`. Methods renamed: `_get_compatible_acquirers` -> `_get_compatible_providers`, `_ensure_acquirer_is_not_disabled` -> `_ensure_provider_is_not_disabled`, `_setup_provider` / `_remove_provider`.

**Computed feature fields.** `support_authorization` -> `support_manual_capture`, `support_fees_computation` -> `support_fees`; `support_refund` and `support_tokenization` also become non-stored, computed by `_compute_feature_support_fields`.

**Availability fields.** `country_ids` -> `available_country_ids`; new `maximum_amount` (Monetary, using new related `main_currency_id`). `_get_compatible_providers` gains an `amount` parameter.

**Accounting logic extracted.** `payment` no longer extends `account.payment`, `account.move`, `account.journal` or `account.payment.method.line`. Consequently `payment.transaction` loses `payment_id`, `invoice_ids`, `invoices_count` and `_create_payment`/`action_view_invoices`; `account.payment` loses `payment_transaction_id`, `payment_token_id`, `source_payment_id`, `refunds_count`, `amount_available_for_refund`, etc. The provider's `journal_id` field disappears and its currency is always the company currency.

**State setters** (`_set_pending`, `_set_authorized`, `_set_done`, `_set_canceled`, `_set_error`, and `_set_canceled` no longer cancelling payments) now return the updated transaction recordset instead of `None`.

## How your habits should change

- Look for **Providers** (not "Acquirers") in the menu, and expect "Provider" wording on transactions and tokens.
- The provider form no longer has a **Payment Journal** field; journal and payment-method lines are managed from accounting configuration.
- Invoice/transaction links (posting invoices, creating payments, reconciliation, transaction links on invoices) now come from the Invoicing/Accounting apps. Install them to keep your existing invoice-based payment flows.
- Country-based availability is configured in the renamed *Countries* field; check it after migration.
- **Custom developments:** renamed fields and methods must be updated, and the new computed support flags replace the stored ones.

## What you gain by migrating

Cleaner terminology, providers that can be limited by order amount and published selectively, a fully-featured demo provider for testing, and a payment module usable standalone (no Invoicing/Accounting required). The separation of payment and accounting also makes the payment flow easier to maintain and to extend with new providers.
