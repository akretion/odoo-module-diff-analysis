# payment migration guide (16.0 -> 17.0)

## What's new for users

- **Payment methods replace providers on payment forms.** Customers now pick from one flat, ordered list of payment methods (Card, PayPal, Bancontact…) instead of choosing a provider first and then a method hidden inside the provider's own page. Each method is linked to the provider that powers it, so the same method can be offered through several acquirers (e.g. PayPal via Mollie, Klarna via Stripe).
- **The chosen payment method is saved on the transaction** (and on the saved token) instead of being lost in the provider's interface, which makes payment follow-up and customer support clearer.
- **Partial capture.** With providers that allow it (Adyen; the Demo provider for testing), you can capture only part of an authorized amount from the sales order or invoice, and void the remainder.
- **Extra fees are gone.** The "customer pays the processing fee" feature (PayPal, Alipay, Demo) was removed: it is forbidden in Europe, cannot be computed accurately per customer, and confused shoppers.
- The separate "Checkout" and "Manage" payment forms were merged into a single, redesigned payment form.
- The payment provider onboarding step is now tracked by the dedicated onboarding module.

## Technical data model changes

- Model `payment.icon` renamed to `payment.method` ("Payment Icon" → "Payment Method").
- `payment.provider`: `payment_icon_ids` → `payment_method_ids`; `show_payment_icon_ids` → `show_payment_method_ids` (this flag is later removed); `display_as` removed; new `action_view_payment_methods()`.
- Fees support dropped: provider fields `fees_active`, `fees_dom_fixed`, `fees_dom_var`, `fees_int_fixed`, `fees_int_var`, `support_fees`, method `_compute_fees()`, constraint `_check_fee_var_within_boundaries()`; transaction field `fees`.
- Provider `support_manual_capture` changed from Boolean to Selection (`full_only`, `partial`).
- `payment.transaction.action_capture()` now opens the new `payment.capture.wizard` when a provider supports partial capture, otherwise it captures immediately. New signatures `_send_capture_request(amount_to_capture=None)` and `_send_void_request(amount_to_void=None)`; `_create_refund_transaction()` replaced by `_create_child_transaction(amount, is_refund=False)`; new `_update_source_transaction_state()`.
- `payment.method` extended with `code`, `active`, `sequence`, `primary_payment_method_id`, `brand_ids`, `is_primary`, `support_tokenization`, `support_express_checkout`, `support_refund`, `supported_country_ids`, `supported_currency_ids`.
- `payment.transaction` and `payment.token` gained a required `payment_method_id`, plus related `payment_method_code`; `payment.token.verified` removed.
- `res.company.payment_provider_onboarding_state` removed, along with `_mark_payment_onboarding_step_as_done()` and `get_account_invoice_onboarding_steps_states_names()`; the step is now handled by `onboarding.onboarding.step`.

## How your habits should change

- When configuring an acquirer, you now manage a list of **Payment Methods** (previously "Payment Icons"); their sequence defines the order customers see.
- Attaching a method to a provider raises a warning to verify that the provider really supports it. Detaching one **archives the tokens** registered with it (irreversible) — Odoo warns you beforehand.
- Remove any extra-fee configuration. If you charged surcharges, move them to order lines or another upstream mechanism.
- Capturing a payment may now go through a wizard asking for the amount (partial capture) instead of always capturing the full amount.
- The payment provider onboarding step is now configured from the onboarding app rather than from company settings.

## What you gain by migrating

- A shorter, clearer checkout with a controlled order of payment methods — fewer duplicated entries and less shopper confusion.
- Reliable payment data: every transaction and saved token records its payment method, easing reconciliation, reporting and support.
- Real control over authorized funds: partial capture and partial void (Adyen), with the remaining amount released on request.
- A leaner codebase: dead fee features removed, onboarding unified, one payment form instead of two.
