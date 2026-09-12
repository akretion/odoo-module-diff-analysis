# payment_payulatam migration guide (14.0 -> 15.0)

## What's new for users

The official 15.0 release notes contain **no PayU Latam-specific item**. The "Payments" chapter only covers other providers (Authorize.Net ACH, Adyen, Buckaroo, Mollie, refunds for Adyen, Stripe tokenization). None of that changes what your shoppers see with PayU Latam, and none of it should be expected for this provider.

What did change is the plumbing: PayU Latam was ported to the payment API introduced in Odoo 15. It now behaves like any other acquirer instead of using its own custom code.

- Checkout is unchanged: the customer is still redirected to the PayU Latam hosted page (sandbox in test mode, production when the acquirer is enabled).
- Every transaction now gets a unique, date-based reference (prefix + timestamp), as PayU Latam requires unique references per merchant account. A retry no longer overwrites the reference of an existing transaction.
- Statuses follow the standard mapping: PENDING -> Pending, APPROVED -> Done, EXPIRED/DECLINED -> Cancelled. An unrecognised status now puts the transaction in **Error** (it used to be Cancelled), so real anomalies are no longer mixed with genuine refusals.
- PayU Latam is now hidden at checkout when the currency is not supported: only ARS, BRL, CLP, COP, MXN, PEN and USD are accepted.
- Still no refunds and no card tokenization for PayU Latam in 15.0; the release-note items about those concern Adyen and Stripe only.

## Technical data model changes

Source: the 14.0 -> 15.0 code diff of the addon.

- **No field was added or removed** on `payment.acquirer`: `payulatam_merchant_id`, `payulatam_account_id` and `payulatam_api_key` still exist.
- `models/payment.py` was deleted and split into `models/payment_acquirer.py` and `models/payment_transaction.py`. The old classes `PaymentAcquirerPayulatam` and `PaymentTransactionPayulatam` disappear; the two `_inherit` hooks on `payment.acquirer` and `payment.transaction` remain, so no data migration is required.
- **Access change:** `payulatam_api_key` is now restricted to `base.group_system` (settings administrators). It was readable by all internal users before. Merchant ID and Account ID lost their group restriction and gained help texts.
- **Removed old-API methods:** `payulatam_form_generate_values`, `payulatam_get_form_action_url`, `_payulatam_form_get_tx_from_data`, `_payulatam_form_get_invalid_parameters`, `_payulatam_form_validate`.
- **Signature:** `_payulatam_generate_sign(values, incoming=True)` replaces `_payulatam_generate_sign(inout, values)`; the amount is now rounded to 1 decimal, matching PayU Latam sandbox behaviour.
- **New overrides:** `_get_compatible_acquirers(..., currency_id=None, ...)` (currency filtering) and `_compute_reference(provider, prefix, separator, **kwargs)` (unique references), plus `_get_specific_rendering_values`, `_get_tx_from_feedback_data` and `_process_feedback_data` replacing the old form methods. Transaction lookup now also filters on `provider = 'payulatam'`, the signature comparison is case-sensitive, and rendering values add `buyerFullName`, a `test` flag and a return URL built from `PayuLatamController._return_url`.

## How your habits should change

- **Credentials:** have an administrator enter the PayU Latam API key; other users no longer see it.
- **Currency:** if you sell in a currency outside the supported list (for example EUR), PayU Latam will simply not be offered. Plan a fallback acquirer.
- **Retries and references:** expect a new unique reference for each payment attempt instead of a rewritten one. Use the reference shown on the transaction to search in the PayU Latam back office.
- **Monitoring:** filter on the Error state as well as Cancelled; error messages such as "Invalid payment status" or "Invalid sign" are now explicit.
- **Custom developments:** any custom code calling the old `payulatam_*` methods must be rewritten against the new API before upgrading.

## What you gain by migrating

- PayU Latam runs on the standard Odoo 15 payment framework, so it benefits from the same flow, statuses and logging as every other provider, and it stays maintained.
- Fewer false positives: unknown statuses, invalid signatures and missing references are reported as errors rather than silently cancelled.
- No duplicate-reference rejections: unique references are generated automatically.
- Unsupported-currency acquirers are hidden, which reduces failed checkouts.
- Field-level security on the API key and clearer configuration labels for Merchant ID and Account ID.
- No database schema change for this addon: the upgrade is mostly a code/configuration exercise.
