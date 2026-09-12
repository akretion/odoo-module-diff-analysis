# account_payment migration guide (15.0 -> 16.0)

Bridges Odoo payments and accounting: journal payment methods, saved tokens, refunds.

## What's new for users

The 16.0 release notes mostly cover other apps (assets, reports, localizations, PoS, spreadsheets). Only the payment items apply here:

- **"Acquirer" is now "Provider".** Everywhere in the interface the payment acquirer is called a payment provider. This is a wording change: a provider may only supply a service, not actually acquire the payment.
- Saved payment tokens are displayed in a layout that fits any screen size.
- Tokens of disabled providers are hidden from the customer portal.
- Providers can be **unpublished** to hide them from customers, and payment forms can be filtered by **maximum payment amount**.
- The **Demo provider** now supports every payment feature (saving cards, manual capture, customer fees, refunds) and lets you choose the test outcome.
- Some providers are **deprecated**: Alipay, Ogone, PayUmoney, PayU Latam.
- Online payment providers can be used **without installing Invoicing/Accounting**: this addon is now an optional bridge, not a prerequisite.
- Grouping the Payments list view now shows total amounts.

Provider-specific novelties (manual capture, refunds, new regional providers) belong to the provider apps, not to this addon.

## Technical data model changes

All changes come from the 16.0 "rename of acquirer to provider" work (odoo/odoo#90899).

- `payment.acquirer` **renamed** to `payment.provider` (file `payment_acquirer.py` -> `payment_provider.py`).
- On the provider, field `provider` (technical code) is renamed `code`; `_compute_journal_id` / `_inverse_journal_id` now rely on `code`.
- On `account.payment.method.line`:
  - `payment_acquirer_id` (Many2one `payment.acquirer`) -> `payment_provider_id` (Many2one `payment.provider`)
  - `payment_acquirer_state` -> `payment_provider_state`
  - `action_open_acquirer_form` -> `action_open_provider_form` (window titled "Provider")
- On `payment.transaction`: `acquirer_id` -> `provider_id`; the text field previously named `provider` is now `provider_code`. `_compute_reference_prefix(provider, ...)` becomes `_compute_reference_prefix(provider_code, ...)`.
- On `payment.token`: `acquirer_id` -> `provider_id` (used by the "Saved Payment Token" field, refund computation and token searches on payments).
- `account.payment`: electronic-method computation and the token onchange read `payment.provider.code`; `_prepare_payment_transaction_vals` sends `provider_id`.
- The journal constraint now queries table `payment_provider` / column `code` (was `payment_acquirer` / `provider`); the error message says "linked to a provider in the enabled or test state".
- Renamed helpers: `_get_default_payment_method_id(code)`, `_get_provider_payment_method(code)`, `_setup_provider(code)`, `_setup_payment_method(code)`, `_remove_provider(code)`.
- No model, field or feature is removed: this is a pure rename/relabel. Existing data is converted by Odoo's upgrade scripts (odoo/upgrade#3542).

## How your habits should change

- On a journal, the payment method line no longer shows "Acquirer": the field and its button are "Provider" and open the provider form.
- The help text on Saved Payment Token now reads "only tokens from providers allowing to capture the amount".
- Filters, group-bys, exports and report templates built on `acquirer_id` / `payment_acquirer_id` must be renamed; SQL must target `payment_provider`, `provider_id` and `code`.
- Everything else is unchanged: register payments, pick a provider payment method, capture or void transactions from the invoice.
- Custom modules (including OCA-style ones) extending `payment.acquirer`, `acquirer_id` or calling the renamed helpers must be ported first.

## What you gain by migrating

- One clear, consistent vocabulary across Accounting, Sales and the portal.
- The 16.0 payment improvements above, available on Odoo Community, without extra work.
- A lighter coupling between payments and accounting (the bridge is optional).
- Upgrade scripts handle the rename, so tokens, history and reconciled payments stay intact; Akretion audits and ports your customizations that still reference acquirer fields.
