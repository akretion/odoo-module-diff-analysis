# payment_adyen migration guide (15.0 -> 16.0)

## What's new for users

Odoo 16.0 brings a set of improvements to the online payments framework that also benefit Adyen users:

- **Two-step payments (manual capture) with Adyen.** You can book (authorize) the amount on the customer's card and capture it later, for example for hotel bookings or pre-orders. It is configured on the provider with the *Capture Amount Manually* setting. Manual capture is exposed through the standard `payment` module (Community).
- **Hidden tokens for disabled providers.** Payment methods (tokens) belonging to a disabled/unpublished provider are no longer shown in the customer portal, so customers cannot pick a card that is no longer usable.
- **Unpublish providers.** A provider can be hidden from customers without archiving it.
- **Filter providers by amount.** You can define the maximum amount up to which a provider is offered on payment forms — useful when Adyen is reserved for larger baskets.
- **No Invoicing/Accounting dependency.** Online payments can now be used without installing the Invoicing/Accounting apps.
- **Better token display.** The payment token list adapts to any screen size.
- **A more capable Demo provider.** The Demo provider supports tokenization, manual capture, customer fees, refunds, and you can pick the outcome of a test payment — handy to rehearse your Adyen flows during the migration.

Note: the rest of the 16.0 "Online Payments" release notes concern other providers (Stripe, AsiaPay, Razorpay, Flutterwave, Mercado Pago, etc.), their deprecation, or Enterprise-only features. Nothing there changes how `payment_adyen` itself behaves.

## Technical data model changes

The dominant change in this addon is a **global rename of "acquirer" to "provider"**, performed upstream in `payment` (PR 90899, task 2842088). It is a breaking change: 2 fields/models removed, 0 added, 1 matched change.

- **Model rename:** `payment.acquirer` → `payment.provider`. The addon file `models/payment_acquirer.py` is now `models/payment_provider.py`, and `PaymentAcquirer` now inherits `payment.provider`.
- **Field rename on the provider:** the selection field `provider` (holding the value `adyen`) is now `code`. All Adyen-specific fields keep their names: `adyen_merchant_account`, `adyen_api_key`, `adyen_checkout_api_url`, `adyen_recurring_api_url`, and the feature-support flags (`support_manual_capture`, `support_refund = 'partial'`, `support_tokenization`).
- **`payment.transaction`:** `acquirer_id` → `provider_id`; `acquirer_reference` → `provider_reference`; the related code field `provider` is now `provider_code` (used in every `if self.provider_code != 'adyen'` guard).
- **`payment.token`:** `acquirer_id` → `provider_id`; `acquirer_ref` → `provider_ref`. `adyen_shopper_reference` is unchanged.
- **Method signature changes:**
  - `_get_tx_from_notification_data(self, provider, notification_data)` → `_get_tx_from_notification_data(self, provider_code, notification_data)`
  - `_create_refund_transaction(..., acquirer_reference=...)` → `provider_reference=...`
- **Wording:** help texts, docstrings, log messages and user-facing strings now say "provider" instead of "acquirer".

Business logic is unchanged: the Adyen API requests (`/payments`, `/refunds`, `/captures`, `/cancels`), currency minor-unit conversion, shopper reference handling, notification processing and refund/void flows behave exactly as in 15.0.

## How your habits should change

- **Vocabulary.** Everywhere in the UI — menus, labels, filters, group-bys, configuration screens, error messages — "acquirer" is now "provider". Old documentation and internal procedures should be updated.
- **Saved filters and exports.** Any saved search, filter, or export built on the acquirer field must be rebuilt: the field is now `code` on the provider and `provider_code` on transactions.
- **Automations.** Server actions, automated actions, and custom Studio automations targeting `payment.acquirer` or the fields `acquirer_id` / `acquirer_reference` / `acquirer_ref` must be repointed to `payment.provider` and `provider_id` / `provider_reference` / `provider_ref`.
- **Custom reports.** Reports or SQL views joining on `acquirer_reference` need to be switched to `provider_reference`.
- **After the migration**, open the Adyen provider and check the merchant account, API key, and API URLs are intact, then verify that existing tokens and past transactions still show their provider reference.

## What you gain by migrating

- A payment stack aligned with Odoo 16.0 terminology and standards — no more confusing "acquirer" wording for a payment service provider.
- Continued upstream maintenance and security fixes for the Adyen integration; 15.0 is no longer the maintained series.
