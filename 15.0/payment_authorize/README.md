# payment_authorize migration guide (14.0 -> 15.0)

## What's new for users

- Authorize.Net now uses the same in-Odoo payment flow as other providers: shoppers pay without being redirected to an Authorize.Net hosted page (15.0 release notes: "new, clean payment flow within Odoo").
- The redirect/hosted-form flow is dropped in favour of direct payment only.
- Refunds (full or partial) can be sent to Authorize.Net from Odoo. If the transaction is not settled yet, Odoo voids it instead of refunding it, as Authorize.Net rejects refunds on unsettled transactions.
- Saved cards are deleted in the Authorize.Net backend when the token is deleted in Odoo, and Odoo warns that a deleted card cannot be restored.
- A single button on the acquirer fetches merchant details, fills the public client key and detects the account currency.
- The release notes also mention ACH payments (USA only) for Authorize.Net; availability depends on your Authorize.Net account and module version - confirm with your integrator.

## Technical data model changes

No new model. The single `models/payment.py` file is split into `payment_acquirer.py`, `payment_token.py` and `payment_transaction.py`; `authorize_request.py` is rewritten. The inherits on `payment.acquirer`, `payment.token` and `payment.transaction` are kept, but the related fields `provider` and `save_token` are removed from `payment.token` (now provided by the payment module).

`payment.acquirer`:
- `authorize_login`: label is now "API Login ID".
- `authorize_transaction_key` / `authorize_signature_key`: restricted to the Settings group (was `group_user`); the signature key is now required.
- `authorize_client_key`: kept, now filled automatically.
- NEW `authorize_currency_id` ("Authorize Currency"): one gateway account per currency; the acquirer is only offered at checkout for the matching currency, and validation transactions use 0.01 in that currency.
- `action_client_secret` and the validity onchange are replaced by `action_update_merchant_details`.

`payment.token`:
- `authorize_profile` kept (labelled "Authorize.Net Profile ID").
- Removed related fields: `provider`, `save_token`.
- New: `_handle_deactivation_request` (deletes the customer profile at Authorize.Net) and `_handle_reactivation_request` (raises an error).

`payment.transaction`:
- Removed: `_authorize_form_*` and `authorize_s2s_*` helpers of the old API.
- New: `_get_specific_processing_values`, `_get_tx_from_feedback_data`, `_process_feedback_data`, `_send_payment_request`, `_send_refund_request`, `_send_capture_request`, `_send_void_request`, `_authorize_tokenize`.

`AuthorizeAPI`:
- New generic helpers `_make_request`, `_format_response`, `_prepare_tx_data`; response codes are handled as strings ('1' approved, '2' declined, '3' error, '4' pending).
- Signature change: `authorize(amount, reference, token=None, opaque_data=None)` and `auth_and_capture(amount, reference, token=None, opaque_data=None)` - they accept opaque card data as well as a token.
- `create_customer_profile(partner, transaction_id)` replaces the opaqueData-based version.
- New methods: `refund`, `delete_customer_profile`, `merchant_details`, `_get_transaction_details`; `get_client_secret()` is replaced by `merchant_details()`.
- `test_authenticate()` now returns the API response dict instead of True/False.

## How your habits should change

- No redirect: nothing to point to the Authorize.Net hosted page. The old signature hashing and relay return URLs are gone.
- After enabling the acquirer, run the merchant details update to fill the client key, then set the Authorize Currency.
- Refunds are available on the transaction; a not-yet-settled payment appears as voided rather than refunded.
- Deleting a saved card removes it permanently at Authorize.Net; it cannot be reactivated.
- Only the currency configured on the acquirer is offered to customers.

## What you gain by migrating

- A consistent checkout experience inside Odoo, maintained by the standard payment framework.
- Native refund, capture and void from the transaction, including partial refunds.
- Automatic token cleanup at Authorize.Net, avoiding stale customer profiles.
- Per-account currency handling that prevents failed payments in unsupported currencies.
- A modern API wrapper instead of the legacy 14.0 code base.
