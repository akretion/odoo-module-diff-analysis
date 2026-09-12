# payment_sips migration guide (14.0 -> 15.0)

## What's new for users

Odoo 15 rewrote the entire payment framework ("new payment API"), and `payment_sips` was migrated in one commit (task-2333041, by Kevin Baptiste).

The Odoo 15 release notes contain **no Sips-specific entry**. The Payments notes only mention Adyen, Buckaroo, Authorize.Net, Mollie and Stripe, and the in-shop payment flows described there are *not* applied to Sips: Sips customers still leave the shop and pay on the Atos/Wordline page. The only generic payment item that also benefits Sips is the new acquirer selector in the payment link wizard.

What actually changes for a Sips user:

- Sips is now a fully "first-class" acquirer of the new payment API, instead of a legacy module using its own flow.
- The acquirer becomes invisible/unavailable when the order currency is not supported by Sips, instead of failing at the very end of checkout.
- Payment errors are reported with clear, explicit messages (wrong amount, unsupported currency, unknown response) instead of a silent "invalid parameters" list.
- Sips transaction references are now always alphanumeric and guaranteed unique per merchant account.
- The Sips secret key is no longer readable by every internal user.

## Technical data model changes

Files and models (from the migration patch):

- `models/payment.py` **deleted**. It contained `AcquirerSips` (`_inherit = 'payment.acquirer'`) and `TxSips` (`_inherit = 'payment.transaction'`).
- Added `models/payment_acquirer.py` (`PaymentAcquirer`, `_inherit = 'payment.acquirer'`) and `models/payment_transaction.py` (`PaymentTransaction`, `_inherit = 'payment.transaction'`). No new tables; `payment.transaction` gains no new field.

`payment.acquirer` fields — same field names kept, behaviours changed:

- `sips_merchant_id`: help text added.
- `sips_secret`: label "Secret Key" → "SIPS Secret Key", and access group changed from `base.group_user` to `base.group_system`.
- `sips_test_url` / `sips_prod_url`: labels "Test url"/"Production url" → "Test URL"/"Production URL".
- `sips_key_version`, `sips_version`, `provider` selection: unchanged.

Methods:

- Removed: `sips_form_generate_values`, `sips_get_form_action_url`, `_sips_form_get_tx_from_data`, `_sips_form_get_invalid_parameters`, `_sips_form_validate`.
- Added: `_get_compatible_acquirers` (acquirer), `_get_specific_rendering_values`, `_get_tx_from_feedback_data`, `_process_feedback_data`, `_sips_data_to_object` (transaction).
- Signature changes: `_sips_generate_shasign(values)` → `_sips_generate_shasign(data)` (now takes the raw string); `_compute_reference(values=None, prefix=None)` → `_compute_reference(provider, prefix=None, separator='-', **kwargs)`.
- Constants: `SIPS_SUPPORTED_CURRENCIES` (namedtuple with ISO id + decimals) → `SUPPORTED_CURRENCIES` (plain ISO code strings). New `RESPONSE_CODES_MAPPING` maps statuses to codes: pending `60`, done `00`, cancel 18 codes (`03`, `05`, `12`, `14`, `17`, `24`, `25`, `30`, `34`, `40`, `51`, `54`, `63`, `75`, `90`, `94`, `97`, `99`).
- Amount validation now uses `to_minor_currency_units` / `to_major_currency_units` and raises a `ValidationError`; the transaction date sent by Sips is no longer parsed nor stored on the transaction.

## How your habits should change

- In the acquirer form, only **System Administrators** can see or edit the secret key. If your accounting team used to read it, give them the right access or have an admin share it.
- References no longer look like invoices. They are sanitized to alphanumerics and end with `x` + a sequence number (e.g. `tx2021...x1`). Searches by invoice/order name in the Sips back-office will not match.
- Don't rely on the Sips transaction date: Odoo keeps its own transaction date.
- Sips will not be offered to customers when the order currency is unsupported; configure a supported currency before going live.
- Unrecognized provider response codes now leave the transaction in **error**, not cancelled. Review such transactions manually.

## What you gain by migrating

- A maintained, supported Sips integration on the current Odoo payment API — the required base for later upgrades (16.0 and beyond).
- Better security: the payment secret is restricted to administrators.
- Fewer dead-end checkouts: incompatible acquirers are hidden before the payment step.
- Fewer rejected transactions: references are sanitized and unique per Sips merchant account.
- Clearer diagnostics for you and your support team when a payment fails.
