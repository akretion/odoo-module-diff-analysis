# payment_alipay migration guide (14.0 -> 15.0)

## What's new for users

The Odoo 15.0 release notes section on Payments covers Mollie (new provider), ACH for Authorize.Net, improved error handling for Adyen and Buckaroo, refunds for Adyen, card tokenization for Stripe, and acquirer selection in the payment link wizard. **Nothing in the release notes concerns Alipay specifically**, so the visible changes for this addon come entirely from its migration to Odoo 15's new payment API (ref. task-2333027).

What a shop user will notice:

- The checkout flow is unchanged: the customer is still redirected to the Alipay gateway page.
- Alipay is now *filtered out at selection time* when it cannot handle the order. For a Chinese "Express Checkout" account (CNY only) paying in another currency, the acquirer simply is not offered anymore, instead of letting the customer start and then failing the transaction.
- Payment feedback from Alipay is validated with clearer, more explicit error messages (wrong amount, wrong currency, wrong seller email, unknown trade status).
- Amounts including fees are now rounded before being sent to Alipay, avoiding entries such as `1118.1999999999998`.
- Configuration labels were reworded for clarity ("Express Checkout (only for Chinese merchants)", "Cross-border") and help texts were added.

## Technical data model changes

No model is added or removed. `payment.acquirer` and `payment.transaction` are still inherited, but the code is now split into `models/payment_acquirer.py` and `models/payment_transaction.py`; the former `models/payment.py` is deleted.

**Fields on `payment.acquirer` (none added, none removed):**
- `alipay_payment_method`: now `required_if_provider='alipay'` (it was optional at model level), labels/help reworded, same selection values.
- `alipay_merchant_partner_id`: still `required_if_provider='alipay'`; the `groups='base.group_user'` restriction was dropped; help text updated (public ID used to identify the account).
- `alipay_md5_signature_key`: still `required_if_provider='alipay'`; visibility tightened from `base.group_user` to `base.group_system`.
- `alipay_seller_email`: `base.group_user` restriction dropped, help text added.

**Methods removed / renamed:**
- `payment.acquirer`: `alipay_compute_fees` removed (fee computation is now handled by the standard payment module); `_get_alipay_urls` -> `_alipay_get_api_url`; `_build_sign` -> `_alipay_build_sign`.
- `payment.transaction`: `_check_alipay_configuration`, `create()` and `write()` overrides removed — the currency compatibility check moved to `_get_compatible_acquirers` on the acquirer; `_alipay_form_get_tx_from_data` -> `_get_tx_from_feedback_data`; `_alipay_form_get_invalid_parameters` removed (checks inlined in `_process_feedback_data`); `_alipay_form_validate` -> `_process_feedback_data`; `alipay_form_generate_values` -> `_get_specific_rendering_values`.
- Status changes now use `_set_done()`, `_set_canceled()`, `_set_error()` instead of `_set_transaction_done()` / `_set_transaction_cancel()` / `_set_transaction_error()` plus `execute_callback()`.
- Transaction lookup by reference now also filters on `provider = 'alipay'`.

## How your habits should change

- **Shoppers:** no change; Alipay remains a redirect-based provider.
- **Settings / accounting users:** an Alipay acquirer now requires the *Account* selection (Cross-border or Express Checkout) in addition to the Merchant Partner ID and MD5 key.
- **Administrators:** the MD5 signature key is only visible to users in the *Settings* (system) group. A non-admin who could read it in 14.0 can no longer do so — make sure the key is stored/backed up somewhere else.
- **Multi-currency shops:** to sell in a currency other than CNY you must use a Cross-border account; Express Checkout will simply not appear for such orders. Configure a second acquirer if you need both.
- **Developers:** any custom module, server action, QWeb template or theme calling `alipay_form_generate_values`, `_alipay_form_validate`, `_alipay_form_get_tx_from_data`, `_alipay_form_get_invalid_parameters`, `alipay_compute_fees` or `_build_sign` must be ported to the new method names/API before upgrading.

## What you gain by migrating

- **Alipay stays supported and maintained**: the addon is rewritten on the payment API that all of Odoo 15 (Community) payments use; staying on 14.0 means staying on a deprecated API that no longer receives fixes.
- **Fewer failed payments at checkout**: incompatible acquirers are filtered automatically instead of producing errors after the customer has paid.
- **Better payment reconciliation and troubleshooting**: precise validation errors and correctly rounded amounts (amount + fees).
- **Tighter security**: the MD5 signing key is restricted to administrators.
