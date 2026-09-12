# payment_adyen migration guide (14.0 -> 15.0)

Adyen support in Odoo 15.0 is a rewrite of the 14.0 integration: the module now talks to the Adyen Checkout API instead of the Hosted Payment Pages (HPP), and plugs into Odoo's token-based payment API.

## What's new for users

- **Direct payment flow in Odoo.** Shoppers no longer leave your website or customer portal to pay on an Adyen-hosted page. Payment method entry happens inline, which reduces drop-off and "I paid but my order still shows unpaid" situations.
- **Clearer error handling.** Adyen result codes are mapped to Odoo transaction states (pending, done, cancelled, error) and the message is stored on the transaction, instead of the old generic "feedback error".
- **Saved payment methods.** Cards can be tokenized by Adyen and reused later (customer portal, subscriptions, recurring charges) through Odoo's standard saved-card mechanism. Deleting one in Odoo also asks Adyen to delete it; a deleted method cannot be restored.
- **Refunds.** Official 15.0 release notes announce partial/full refunds for Adyen, but the addon code changes reviewed here contain no refund logic — check with your integrator before promising this.

## Technical data model changes

`models/payment.py` was deleted and replaced by `payment_acquirer.py`, `payment_token.py` and `payment_transaction.py`. Older classes (`AcquirerAdyen`, `TxAdyen`) become `PaymentAcquirer`, `PaymentToken`, `PaymentTransaction`.

`payment.acquirer`
- Removed: `adyen_skin_code` (Skin Code) and `adyen_skin_hmac_key` (Skin HMAC Key).
- Kept: `adyen_merchant_account` (Merchant Account), now restricted to Settings users (`base.group_system`).
- Added: `adyen_api_key`, `adyen_hmac_key`, `adyen_checkout_api_url`, `adyen_recurring_api_url` (all required for Adyen).
- `create`/`write` now normalise the two API URL fields (version/endpoint suffix stripped).

`payment.transaction`
- Added `adyen_payment_data` (readonly, administrators only). Adyen data is matched on the merchant reference and processed through `_get_tx_from_feedback_data` / `_process_feedback_data`.

`payment.token` (new inheritance)
- Added `adyen_shopper_reference` (readonly). Deactivation calls the Recurring API `/disable` endpoint; reactivation raises an error.

Methods
- Removed (form/HPP based): `adyen_form_generate_values`, `adyen_get_form_action_url`, `_adyen_generate_merchant_sig(_sha256)`, `_adyen_convert_amount`, `_adyen_form_get_tx_from_data`, `_adyen_form_get_invalid_parameters`, `_adyen_form_validate`.
- Added: `_adyen_make_request`, `_adyen_compute_shopper_reference`, `_get_specific_processing_values`, `_send_payment_request`, `_get_tx_from_feedback_data`, `_process_feedback_data`, `_adyen_tokenize_from_feedback_data`.

## How your habits should change

- **Configuration:** forget Skin Code and Skin HMAC Key. Enter the Adyen Merchant Account, API Key, webhook HMAC Key, and the base URLs of the Checkout and Recurring APIs (e.g. `https://checkout-test.adyen.com`). Paste only the base URL: a `/Vxx/...` suffix is stripped automatically and the right version/endpoint is appended by Odoo.
- **Credentials are administrator-only:** Merchant Account, API Key and HMAC Key are now limited to the Settings group, so regular users can no longer read them.
- **No more merchant signature checks:** the old `merchantSig` validation disappears; notifications are matched by merchant reference and validated with the configured HMAC key.
- **Shopper journey:** no redirect to `pay.shtml`; the payment is prepared in Odoo and sent to Adyen server-side.
- **Saved cards:** they behave as standard Odoo payment tokens; a failed deletion at Adyen never blocks the Odoo-side action.

## What you gain by migrating

- A modern Adyen integration based on Adyen's supported Checkout API rather than the legacy Hosted Payment Pages flow.
- Fewer lost sales: no external redirect, inline payment entry, clearer retry paths after a refused payment.
- Reliable transaction statuses and error messages, making reconciliation and customer support easier.
- Reusable saved cards for subscriptions and repeat customers, with proper deactivation handling.
- Tighter security: Adyen credentials are limited to administrator users.
