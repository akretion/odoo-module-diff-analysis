# payment migration guide (14.0 -> 15.0)

## What's new for users

The `payment` module is the framework behind every online payment in Odoo. In 15.0 it received a deep refactoring whose visible effects are:

- **Journals and acquirers are decoupled.** Several payment acquirers can now be registered on the same bank journal. The acquirer's journal is no longer created for it: it is derived from the payment method line configured on the journal for that provider.
- **Simpler token (card saving) configuration.** The old "Save Cards" choice (Never / Let the customer decide / Always) is replaced by a single "Allow Saving Payment Methods" switch on the acquirer.
- **Payment methods on journals are protected.** You can no longer delete a payment method line used by an acquirer in Test or Enabled state; Odoo lists the concerned acquirers.
- **Provider field.** A new acquirer defaults to "No Provider Set" instead of the old "Custom Payment Form".
- **Two payment form templates per acquirer**: a redirect form (the customer leaves your site) and an inline form (payment embedded in your page), instead of the old single "form button template" and "S2S form template".
- **Cleaner payment flows** for the PSPs shipped by Odoo.

Other 15.0 release-note items (Adyen refunds, Buckaroo error handling, Mollie, Authorize.Net ACH, Stripe redirect tokenization, Payment Link) concern specific provider modules — some Enterprise-only — and are out of scope of this Community addon, which only covers the shared payment framework.

## Technical data model changes

**Models**
- `payment.token`, `payment.transaction` and `payment.icon` now live in their own files; `account_invoice.py` became `account_move.py`; `chart_template.py` was dropped, replaced by `account_journal.py`.
- `account.chart.template` is no longer inherited: journals are no longer created at chart installation. `create_missing_journals`, `_create_missing_journals`, `_get_journal_create_values` and `_get_journal_search_domain` were removed.

**Fields**
- `payment.acquirer`: added `allow_tokenization`, `redirect_form_view_id`, `inline_form_view_id`; removed `save_token`, `token_implemented`, `authorize_implemented`, `fees_implemented`, `payment_flow`, `view_template_id`, `registration_view_template_id`, `check_validity`, `qr_code`, `color`, `image`, `image_payment_form`; `provider` now defaults to `none`; `journal_id` becomes computed/inverse (domain `type = 'bank'`); ordering is now `module_state, state desc, sequence, name`.
- `payment.transaction`: `payment_token_id` renamed to `token_id`; technical fields (`date`, `type`, `html_3ds`, `return_url`, `is_processed`, `invoice_ids_nbr`) removed.
- `payment.token`: `payment_ids` → `transaction_ids`; `short_name` is computed; `company_id` stored and indexed.
- `account.payment`: `related_partner_ids` replaced by `suitable_payment_token_ids` and `use_electronic_payment_method`; transactions are created with `operation='offline'` and `token_id`.
- `account.move`: `_create_payment_transaction` removed; `get_portal_last_transaction()` now uses `transaction_ids._get_last()`.

**Methods**
- Old S2S API (`s2s_do_transaction`, `s2s_capture_transaction`, `s2s_void_transaction`, `_post_process_after_done`) replaced by `_send_payment_request`, `_send_capture_request`, `_send_void_request`, `_finalize_post_processing`, `_get_last`.
- `_prepare_payment_transaction_vals(**extra_create_values)` and `_create_payment_transaction(**extra_create_values)` signatures changed.
- New `account.journal._check_inbound_payment_method_line_ids` constraint and `payment.acquirer._get_default_payment_method`.

## How your habits should change

- Stop creating or assigning one journal per acquirer. Keep at least one payment method line per provider on the journal and select the journal from there; constraints will warn you on inconsistencies.
- Replace your "Save Cards" policy with the "Allow Saving Payment Methods" switch.
- Only bank journals are accepted for acquirers; re-check any acquirer pointing to a cash journal.
- References to `payment_token_id`, `related_partner_ids` or `payment_ids` in filters, exports or automated actions must be updated.
- If you use a custom or third-party acquirer module, it must be ported to the new API (method names above), the `token_id` field and the new form templates.

## What you gain by migrating

- A single, consistent payment API aligned with 15.0, with fewer provider-specific quirks.
- More accounting flexibility: several acquirers in one journal, payment methods filtered per acquirer, and protection against deleting a payment method still in use.
- Simpler card-saving configuration and support for inline (on-page) payment forms instead of redirects.
