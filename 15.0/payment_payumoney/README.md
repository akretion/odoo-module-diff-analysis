# payment_payumoney migration guide (14.0 -> 15.0)

## What's new for users

The official Odoo 15.0 release notes do not mention PayUmoney at all. Their Payments section only covers other providers (Authorize.Net ACH, Adyen/Buckaroo error handling, Mollie, Stripe tokenization, Adyen refunds, payment links) and describes no change to the PayUmoney customer journey. Everything relevant here therefore comes from the code changes. What you will actually notice:

- **Currency restriction.** Acquirers are now filtered by currency: a PayUmoney acquirer is only offered when the transaction currency is INR. Shops selling in other currencies will no longer see PayUmoney at checkout.
- **Merchant Salt is now confidential.** The salt is restricted to system administrators (Settings group) instead of every internal user.
- **Clearer failures.** A failed payment now reports the PayU error code returned by the provider rather than a generic cancellation.
- The checkout flow itself is unchanged: the shopper is still redirected to the PayU hosted page and comes back to Odoo.

## Technical data model changes

- `models/payment.py` (137 lines) was deleted and split into `models/payment_acquirer.py` and `models/payment_transaction.py`.
- Both classes keep their `_inherit` (`payment.acquirer` and `payment.transaction`). No table or model is dropped; existing acquirer and transaction records are preserved.
- Fields: `payumoney_merchant_key` gained a help text; `payumoney_merchant_salt` changed from `groups='base.group_user'` to `groups='base.group_system'`.
- New override `_get_compatible_acquirers(...)`: removes PayUmoney acquirers when the currency is not INR.
- `_payumoney_generate_sign(inout, values)` becomes `_payumoney_generate_sign(values, incoming=True)`; the hashed key list also changed.
- Transaction methods replaced: `_payumoney_form_get_tx_from_data` → `_get_tx_from_feedback_data(provider, data)`; `payumoney_form_generate_values` → `_get_specific_rendering_values`; `_payumoney_form_validate` → `_process_feedback_data`. `_payumoney_form_get_invalid_parameters` is removed; amount/reference checks now run in the shared payment flow.
- Status handling uses the generic setters `_set_done()`, `_set_error()`, `_set_pending()` instead of `_set_transaction_*`.
- `firstname` / `lastname` are split from the partner name via `payment_utils.split_partner_name`; the return URL points to the new `PayUMoneyController._return_url`; `ValidationError` now comes from `odoo.exceptions`.

## How your habits should change

- Configure the acquirer as a system administrator: ordinary users can no longer read the merchant salt.
- Review your currencies. If you sell in anything other than INR, PayUmoney will no longer be offered as a payment option.
- Read the transaction log for failure causes — the PayU error code is now stored on the transaction.
- If you customized `_payumoney_generate_sign`, `payumoney_form_generate_values`, `_payumoney_form_validate` or `_payumoney_form_get_tx_from_data` in your own modules or templates, those overrides must be rewritten against the new names and signatures.
- No re-entry of credentials is needed: the merchant key and salt themselves are unchanged.

## What you gain by migrating

- **Support and continuity.** PayUmoney runs on the same payment API as every other provider in Odoo 15.0, so it keeps receiving fixes and stays compatible with the rest of the payment ecosystem instead of living on a deprecated code path.
- **Safer credentials.** The merchant salt is now treated as a secret, limited to administrators.
- **Fewer failed or mismatched payments.** The INR-only guard prevents offering PayUmoney where it cannot work, and the reference/signature verification was strengthened.
- **Faster troubleshooting.** Error codes from PayU are surfaced directly on the transaction.
- **Cleaner maintenance.** Transaction feedback is handled by the shared flow, so custom developments and future upgrades are simpler.

If you only pay in INR with a standard configuration, the migration is essentially transparent for your users; the work is on the technical side (custom code and access rights).
