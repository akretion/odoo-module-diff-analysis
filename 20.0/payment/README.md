# payment migration guide (19.0 -> 20.0)

## What's new for users

No official release-note extract matched this addon; the overview below comes from the code changes themselves.

- Provider configuration simplified: the old Disabled / Enabled / Test Mode selector is gone. A provider is available as soon as its module is installed and the record is not archived. Two controls remain: **Live** (on = real transactions, off = test mode) and the standard archive flag.
- Payment methods now belong to a single provider. They are created and edited from the provider form, can no longer be shared between providers, and are archived with their provider.
- Provider dashboard: processed amount, transaction count and token count are shown on the provider form, with buttons to open the related records.
- Feature support (tokenization, express checkout, manual capture, refund) is now computed, reflecting what the provider module really offers.
- Duplicating a provider also copies its payment methods and brands.
- Fewer double charges: incoming provider data is no longer applied immediately; it is queued and processed serially by a cron, so concurrent updates of the same transaction can no longer replay a non-idempotent call. Pending updates are visible on the transaction and a failed payload can be handled with "Mark as Confirmed".

## Technical data model changes

- New model **`payment.data`** (`transaction_id`, `payload`, `errored`, `error_traceback`) processed by `_cron_process`.
- **`payment.provider`**: `state` and `color` removed; `is_live` and `active` added. `payment_method_ids` becomes a One2many on the new `provider_id`; new `primary_payment_method_ids`, `payment_transaction_ids`, `payment_token_ids` and computed `processed_amount`, `transaction_count`, `token_count`. `_get_compatible_providers` → `_find_available_providers`; new `_find_available_payment_methods`, `_find_available_tokens`, `_get_pm_from_code`, `action_add_payment_method`, `action_view_payment_transactions/tokens`.
- **`payment.method`**: `provider_ids` Many2many → `provider_id` Many2one (required, cascade), unique constraint on (provider_id, code). New `_deduplicate_by_code`, `_sort_by_display_order`, `_is_postpaid`; removed `_get_from_code`, `_get_compatible_payment_methods` and the "warn before attaching a payment method" onchange.
- **`payment.transaction`**: new `payment_data_ids` / `payment_data_count`; `_process(provider_code, payment_data)` → `_process(payment_data)`; new `_record`, `action_confirm`, `action_post_process`, `action_view_payment_data`; `_ensure_provider_is_not_disabled` → `_ensure_provider_is_active`; `write()` now requires the `payment_safe_write=True` context key.
- **`payment.token`**: `_get_available_tokens` removed (replaced by the provider helper). **`res.partner`**: `_get_payment_tokens` added. **`res.company`**: payment onboarding helpers added.

## How your habits should change

- To stop using a provider, archive it: there is no "Disabled" state anymore.
- Use the Live toggle to switch between test and production. Toggling it archives the provider's tokens.
- Manage payment methods from the provider form. They can no longer be enabled from a global list, nor enabled while no supporting provider is installed.
- Avoid direct writes on `payment.transaction` (imports, server actions, custom code). Use `_record()` to queue incoming provider data, or pass `payment_safe_write=True` when the write is safe to replay.
- Provider modules must ship their own payment method records.

## What you gain by migrating

- **More reliable payments**: serialized payload processing removes the race conditions that could charge a customer twice while Odoo recorded one transaction.
- **Clearer configuration**: one live/test switch, archive instead of disable, and methods tied to the provider that actually supports them.
- **Better visibility**: per-provider amounts, transaction and token counters on the form.
- **A safety net**: failed payloads are kept and flagged, and stuck transactions can be confirmed manually.
