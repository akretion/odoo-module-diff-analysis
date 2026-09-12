# payment_authorize migration guide (15.0 -> 16.0)

## What's new for users

From the official Odoo 16.0 release notes, the items that touch this addon are:

- **Authorize.Net refunds.** Online payments made with Authorize.Net can be refunded from Odoo or from the Authorize.Net dashboard. In Odoo the refund is full only (the addon declares `support_refund = 'full_only'`).
- **Disabled providers hide their tokens.** Payment methods (tokens) belonging to disabled providers are hidden from the customer portal.
- **Unpublish providers.** You can hide a provider from customers without disabling it for internal use.
- **Filter providers by amount.** You can choose which providers are shown on a payment form based on a maximum payment amount.
- **No Invoicing/Accounting needed.** Online payment providers can now be used without installing the Invoicing/Accounting apps.
- **Token display.** The payment-token display was adapted to fit any screen size.

The release notes also cover features for other providers (Stripe/Adyen manual capture, Stripe express checkout, new providers such as Flutterwave, Razorpay, Mercado Pago, AsiaPay, Amazon Payment Services, and the deprecation of Alipay/Ogone/PayUmoney/PayU Latam). They are unrelated to Authorize.Net and are not part of this addon.

## Technical data model changes

Everything below comes from the single 16.0 commit "rename of acquirer to provider" (odoo/odoo#90899). No new fields or models are added by this addon, and the way Authorize.Net is contacted is unchanged.

Models and files:
- `payment.acquirer` is renamed to `payment.provider`; `models/payment_acquirer.py` becomes `models/payment_provider.py`; class `PaymentAcquirer` becomes `PaymentProvider`; `_inherit = 'payment.acquirer'` becomes `_inherit = 'payment.provider'`.

Fields:
- On the provider: `provider` (Selection) is renamed to `code`. The value stays `('authorize', 'Authorize.Net')`, so all tests `provider == 'authorize'` become `code == 'authorize'`.
- On `payment.token`: `acquirer_id` -> `provider_id`, `acquirer_ref` -> `provider_ref`.
- On `payment.transaction`: `acquirer_id` -> `provider_id`, `acquirer_reference` -> `provider_reference`, and the code field `provider` -> `provider_code`.

Method signatures:
- `_get_compatible_acquirers(...)` -> `_get_compatible_providers(...)`.
- `_get_tx_from_notification_data(self, provider, ...)` -> `...(self, provider_code, ...)`; the search domain `('provider', '=', 'authorize')` becomes `('provider_code', '=', 'authorize')`.
- `AuthorizeAPI.__init__(acquirer)` -> `__init__(provider)` (internal only).
- User-facing error messages now say "provider" instead of "acquirer".

Database columns are renamed by Odoo's upgrade scripts (related odoo/upgrade#3542), so existing tokens and transactions are carried over.

## How your habits should change

- Vocabulary: everywhere Odoo used to say "acquirer", 16.0 says "provider" — in settings, list views, filters, and export columns.
- Saved filters, exports, automated actions, and custom reports built on `payment.acquirer`, `acquirer_id`, `acquirer_ref` or `acquirer_reference` must be rewritten with the new names, otherwise they return nothing.
- Custom code must follow the method renames (`_get_compatible_acquirers`, `_get_tx_from_notification_data`) and read `provider_code` instead of `provider` on transactions.
- Refunds stay full-only. If your process allows partial refunds, keep doing them on the Authorize.Net side.
- If you disable a provider to stop new online payments, customers will no longer see its saved cards in the portal — the intended behaviour, but it can surprise customers with saved tokens.

## What you gain by migrating

- A supported version: 16.0 keeps you on the maintained Odoo release and on the current payment API.
- Straightforward upgrade: the addon change is a naming refactor, and Odoo's upgrade scripts rename the columns for you.
- Refunds can be triggered from Odoo as well as from the Authorize.Net dashboard.
- A cleaner customer portal, with no stale tokens from providers you switched off.
- More control over what customers see: publish/unpublish providers and filter them by maximum amount.
- Payment features no longer force the installation of Invoicing/Accounting.
