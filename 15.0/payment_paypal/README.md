# payment_paypal migration guide (14.0 -> 15.0)

## What's new for users

The 15.0 release notes contain **no PayPal-specific entry**. The *Payments* chapter of the release notes covers other providers only (Adyen refunds and error handling, Buckaroo, Mollie, Authorize.Net ACH, Stripe tokenization); none of these change how PayPal behaves.

One framework-level improvement does reach PayPal indirectly: the **payment link wizard now offers the payment acquirer as a dropdown**, so you can build a payment link that forces PayPal.

For PayPal itself, the shopper experience is **unchanged**: the customer is still redirected to PayPal's website (or its sandbox) to pay, then returns to your shop. The 15.0 work on this addon is an internal migration to the new payment API — not a new checkout design.

On the administration side, credentials are now locked down: **Merchant Account ID** and **PDT Identity Token** are reserved to Settings administrators, while the PayPal business email remains available to the users who read the acquirer.

## Technical data model changes

**Files / classes reorganised**: `models/payment.py` is split into `models/payment_acquirer.py` and `models/payment_transaction.py`; `AcquirerPaypal` becomes `PaymentAcquirer` and `TxPaypal` becomes `PaymentTransaction` (same two inherited models).

**`payment.acquirer` (PayPal)**
- **Removed** the PayPal default fee fields: `fees_dom_fixed`, `fees_dom_var`, `fees_int_fixed`, `fees_int_var`. PayPal no longer ships its own default fee grid; the generic fee fields/computation take over.
- `paypal_email_account`: still `required_if_provider='paypal'`, but the `base.group_user` group restriction is dropped (new help text); access now follows the acquirer record's own access rights.
- `paypal_seller_account`: group changed from `base.group_user` to `base.group_system`.
- `paypal_pdt_token`: group changed from `base.group_user` to `base.group_system`.
- `paypal_use_ipn`: still defaults to True, group restriction dropped.
- **Removed methods**: `_get_feature_support`, `_get_paypal_urls`, `paypal_compute_fees`, `paypal_form_generate_values`, `paypal_get_form_action_url`.
- **Added methods**: `_paypal_get_api_url()` (returns the production or sandbox `webscr` URL based on the acquirer state) and `_paypal_send_configuration_reminder()` (the "add your PayPal account" email).

**`payment.transaction` (PayPal)**
- **Field renamed**: `paypal_txn_type` ("Transaction type") becomes `paypal_type` ("PayPal Transaction Type"). It now stores `txn_type` instead of `payment_type`, and its help states it is only useful for debugging.
- **Removed methods**: `_paypal_form_get_tx_from_data`, `_paypal_form_get_invalid_parameters`, `_paypal_form_validate`.
- **Added the new-API hooks**: `_get_specific_rendering_values`, `_get_tx_from_feedback_data`, `_process_feedback_data`.
- The transaction now also carries the partner language (`lc`), and a `notify_url` is sent only when IPN is enabled.

## How your habits should change

- **Checkout is unchanged** — still a redirect to PayPal.
- If you relied on PayPal's default fee values, **review your acquirer fee configuration** after migration: those defaults are no longer provided by this addon.
- Only **Settings administrators** can see or edit the Merchant Account ID and PDT token; grant Settings access to whoever configures PayPal.
- Transaction type now lives in `paypal_type`. Any custom report, export or spreadsheet reading `paypal_txn_type` must be updated.
- Custom code calling `paypal_get_form_action_url`, `paypal_compute_fees` or the old `paypal_form_*` methods must be ported to the new API hooks — a developer task, not a settings change.

## What you gain by migrating

- PayPal runs on the **unified 15.0 payment API** shared with every other acquirer: one consistent flow and status handling driven by the generic payment engine.
- **Payment links can now force PayPal** through the acquirer dropdown.
- **Tighter security**: credentials restricted to administrators, business email visible to the staff who need it.
- **Less PayPal-specific code** to maintain: fees, URLs and validation are shared with the payment module.
- Remaining on 14.0 keeps you on a **deprecated payment API** that later versions keep removing; migrating now avoids a larger jump later.
