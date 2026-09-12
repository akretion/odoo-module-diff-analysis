# payment_ogone migration guide (15.0 -> 16.0)

## What's new for users

- **Ogone is deprecated in 16.0.** The module still ships and keeps working, but it is no longer developed and Odoo announced it will be removed in a future version. The 16.0 upgrade is the right moment to plan a replacement provider or to scope long-term support for Ogone.
- **"Acquirer" is renamed "Provider" everywhere in the UI.** Your Ogone settings (PSPID, User ID, Password, SHA key) are unchanged; only the naming and the screens change.
- Online payment providers can now be used without installing Invoicing/Accounting.
- Providers can be unpublished to hide them from customers, and filtered on payment forms by maximum payment amount.
- Payment methods (tokens) of disabled providers are hidden from the customer portal; the token display was reworked to fit any screen.

The release notes list no other Ogone-specific novelty for 16.0: apart from the deprecation notice, the change is the acquirer → provider rename described below.

## Technical data model changes

Source: the 15.0 → 16.0 diff of `payment_ogone` (PR odoo/odoo#90899). One file renamed; no model added or removed.

- `models/payment_acquirer.py` → `models/payment_provider.py`.
- `PaymentAcquirer` (`_inherit = 'payment.acquirer'`) → `PaymentProvider` (`_inherit = 'payment.provider'`).
- On the provider model, the selection field `provider` → `code` (`selection_add=[('ogone', "Ogone")]` unchanged).
- `payment.transaction`: `acquirer_id` → `provider_id`, `acquirer_reference` → `provider_reference`, generic field `provider` → `provider_code`; token creation writes `provider_id` / `provider_ref`.
- `payment.token`: `acquirer_id` → `provider_id`, `acquirer_ref` → `provider_ref`.
- Method signatures:
  - `_get_compatible_acquirers(...)` → `_get_compatible_providers(...)` (Ogone is still excluded from validation operations);
  - `_compute_reference(provider, ...)` → `_compute_reference(provider_code, ...)` (40-character reference rule unchanged);
  - `_get_tx_from_notification_data(provider, ...)` → `_get_tx_from_notification_data(provider_code, ...)`, search now on `provider_code`.
- Ogone configuration fields (`ogone_pspid`, `ogone_userid`, `ogone_password`, `ogone_shakey`) and the DirectLink / hosted payment page behaviour are untouched.

## How your habits should change

- Rebuild saved filters, group-bys, imports/exports and views that referenced *Acquirer* or the technical field `provider`, using *Provider* / `provider_code` instead.
- Update custom reports, spreadsheets or QWeb templates using `acquirer_id` / `acquirer_reference` to `provider_id` / `provider_reference`.
- Developers: adapt modules inheriting the renamed methods; the reference and notification entry points now receive a provider *code*, not a record field.
- If Ogone is business-critical, treat the upgrade as a decision point: keep a deprecated provider, or move to a maintained one.

## What you gain by migrating

- One standardised payment API for every provider: less custom code and easier provider changes later.
- Customer-facing controls: unpublish providers, hide tokens of disabled providers, filter providers by amount.
- Payments usable without Invoicing/Accounting installed.
- A supported Odoo version and a clean `payment_provider` technical base instead of the legacy acquirer naming.
