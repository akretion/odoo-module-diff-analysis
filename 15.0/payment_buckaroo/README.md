# payment_buckaroo migration guide (14.0 -> 15.0)

## What's new for users

The 15.0 release notes mention a single Buckaroo item: **"Improve the handling of payment errors with PSP Buckaroo."** That is exactly what this migration delivers.

Practically, when Buckaroo returns a status code Odoo does not recognize (wrong code, missing code, unexpected feedback), the transaction is now flagged with an explicit error message ("Buckaroo: Unknown status code: 123") and the reason is logged, instead of being silently marked as *Cancelled*. Troubleshooting a failed customer payment therefore no longer requires guessing whether the shopper cancelled or whether Buckaroo sent something unexpected.

Nothing else in the release notes concerns this addon. The other payments headlines (Mollie as a new provider, Adyen refunds, Stripe card tokenization, Authorize.Net ACH) relate to other modules, and some are Enterprise-only: they are **not** part of this Community migration.

## Technical data model changes

The addon was rewritten for the new Odoo 15 payment API. The single `models/payment.py` file was split into `payment_acquirer.py` and `payment_transaction.py`.

Removed fields on `payment.acquirer`:
- `brq_websitekey` ("WebsiteKey")
- `brq_secretkey` ("SecretKey")

Added fields on `payment.acquirer`:
- `buckaroo_website_key` ("Website Key") — same purpose as `brq_websitekey`.
- `buckaroo_secret_key` ("Buckaroo Secret Key") — replaces `brq_secretkey`, and its visibility is tightened from `base.group_user` to `base.group_system`.

Model inheritance is unchanged (`payment.acquirer`, `payment.transaction`), but the old `AcquirerBuckaroo` / `TxBuckaroo` classes are gone.

Method signature deltas:
- `_get_buckaroo_urls(environment)` → `_buckaroo_get_api_url()` (derived from acquirer state).
- `_buckaroo_generate_digital_sign(inout, values)` → `_buckaroo_generate_digital_sign(values, incoming=True)`.
- `buckaroo_form_generate_values` → `_get_specific_rendering_values`.
- `_buckaroo_form_get_tx_from_data`, `_buckaroo_form_get_invalid_parameters`, `_buckaroo_form_validate` → `_get_tx_from_feedback_data` / `_process_feedback_data` (new API entry points).
- State setters `_set_transaction_done/pending/cancel` → `_set_done/_set_pending/_set_canceled`.

Behaviour changes:
- Transaction lookup now also filters on `provider = 'buckaroo'`.
- `BRQ_PAYMENT` is no longer required; only reference and signature.
- `acquirer_reference` keeps the first key of the comma-separated `BRQ_TRANSACTIONS`.
- Signature building now only includes keys starting with `add_`, `brq_` or `cust_` (case-insensitive), sorted by upper-cased key; incoming values are URL-decoded before checking.
- The four return URLs (return / cancel / error / reject) all now point to the same Odoo return URL, but all four keys are still sent because they are part of the signature.
- Previously handled error/reject status codes (490/491/492/690) are no longer mapped; they fall into the generic "unknown status code" error handling described above.

## How your habits should change

- No action needed on the payment flow itself: the customer is still redirected to the Buckaroo checkout page, as in 14.0.
- If you configured the acquirer through the database or an import script, update `brq_websitekey` / `brq_secretkey` to the new names.
- Only system administrators see the secret key from now on. Inform your functional team if they used to read it.
- When a payment fails with an unusual status, read the transaction's error message first: it is now explicit rather than a plain cancellation.
- Re-test the acquirer in test mode after migrating.

## What you gain by migrating

- Clearer, actionable payment errors instead of silent cancellations.
- Alignment with the Odoo 15 payment framework, the base for all future provider improvements and fixes.
- A safer secret-key restriction (system administrators only).
- A cleaner code base split per model, easier to maintain and extend.
- Staying on 14.0 means no further Buckaroo fixes, since the provider code was fully rewritten for 15.0.
