# payment migration guide (19.0 -> 20.0)

Scope: the Community `payment` addon. Provider-specific modules (`payment_stripe`, etc.) and Enterprise-only features are out of scope.

## What's new for users

**Payment provider screens reworked.** The provider form has been reorganised to highlight the supported payment methods and simplify configuration. A provider is now available as soon as its module is installed and the provider is not archived. The old "Disabled / Enabled / Test Mode" state has been removed: you use a **Live** toggle to switch between test and production, and the standard **Archive** action when a provider is no longer used. Publishing on the website follows the Live toggle.

**Payment methods are now provider-specific.** A payment method belongs to exactly one provider instead of being a shared catalogue entry linked to several providers. Methods are created and edited from the provider form ("Add Payment Method"), and activating or deactivating a method applies to its brands too. Archiving a provider deactivates its methods and archives its tokens.

**Payment confirmations go through a queue.** Payment data received from a provider is stored first and processed serially by a dedicated cron, instead of being applied immediately by whichever request received it. Customers see no difference: the confirmation page still triggers immediate processing. If processing fails, the payload is kept flagged as "Failed", the transaction is not confirmed, and a **Mark as Confirmed** action lets you unblock it manually.

The release notes also list many new provider integrations and payment methods (PayPal card payments, new Stripe/Mollie methods, new providers such as Toss Payments, PayU, ECPay...). Those live in their own modules, not in `payment` itself.

## Technical data model changes

New model:
- `payment.data` — pending payload: `transaction_id`, `payload` (JSON), `errored`, `error_traceback`; processed by `payment.processing_cron`.

`payment.provider`:
- Removed `state` and `color`. Added `is_live`, `active`, `processed_amount`, `transaction_count`, `token_count`, `payment_token_ids`.
- `payment_method_ids`: Many2many → One2many (inverse `provider_id`); new computed `primary_payment_method_ids`.
- `sequence` default is now 1000; `support_manual_capture` no longer offers "none".
- New `copy()` duplicates methods and brands; new `action_add_payment_method`; `action_view_payment_methods` → `action_view_payment_transactions`; `_deactivate_unsupported_payment_methods` and `_toggle_post_processing_cron` removed.
- `_get_compatible_providers(...)` → `_find_available_providers(...)`, whose arguments after `amount` are now keyword-only.

`payment.method`:
- `provider_ids` (Many2many) → `provider_id` (Many2one, required, cascade, indexed); new SQL constraint unique on (`provider_id`, `code`).
- `active` no longer defaults to True; `sequence` default 1 → 1000.
- `_get_compatible_payment_methods` and `_get_from_code` removed; new `_deduplicate_by_code`, `_sort_by_display_order`; `_check_manual_capture_supported_by_providers` → `..._by_provider`.
- The onchange warning before attaching a method to a provider and the "cannot delete a method linked to a provider" check are gone.

`payment.transaction`:
- Added `payment_data_ids` (One2many) and `payment_data_count`.
- `write()` now raises unless the context key `payment_safe_write=True` is set.
- `_process(provider_code, payment_data)` → `_process(payment_data)`; `_record(payment_data)` is the single entry point for incoming provider data; new `_run_processing()`.
- Added `action_confirm` and `action_view_payment_data`.

## How your habits should change

- Manage payment methods on the provider form; there is no longer a central list of methods shared across providers.
- To stop offering a provider, **archive** it rather than setting a state; use **Live** for test/live switching, and expect a warning that linked tokens are archived when you toggle it.
- If custom code or a connector writes to `payment.transaction`, it must pass `payment_safe_write=True` in the context, or better, use `_record()` to queue the payload.
- When a payment stays unconfirmed, open the transaction's **Pending Updates** smart button, filter on "Failed", check the traceback, then use **Mark as Confirmed** if the payment really succeeded.
- Automation that reads `state` on providers or `provider_ids` on methods must be rewritten.

## What you gain by migrating

- **No more double charges:** serialised processing removes concurrent writes and the rollback/replay that could re-send a non-idempotent provider call.
- **Traceability:** failed payloads are stored with their traceback instead of being lost, with a manual unblock action.
