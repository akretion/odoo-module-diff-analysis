# account_edi migration guide (13.0 -> 14.0)

## What's new for users

Odoo 14.0 rebuilds how the `account_edi` addon tracks electronic documents. In 13.0 an EDI was just an attachment glued to an invoice; in 14.0 every invoice (and now every payment) carries one **EDI document** per format, with its own lifecycle: *To Send*, *Sent*, *To Cancel*, *Cancelled*.

What this means day to day:

- **Status is visible.** Journal entries show an aggregated EDI state plus an error counter, and the accounting list views put more emphasis on document status and next activities, so you can spot what still needs attention.
- **Errors are handled.** If a web-service call fails, the error message is stored on the document and shown on the invoice instead of failing silently.
- **Payments are covered.** Formats can now require an electronic document for a payment too, not only for invoices.
- **Two speeds.** A format can be *synchronous* (the file is produced when you post) or *asynchronous* (a scheduled action/CRON talks to the government web service in the background). You can also trigger processing manually from the invoice or payment.
- **Cancellation is a workflow.** A posted invoice whose EDI has already been sent can no longer be reset to draft; you request an EDI cancellation, and once accepted the invoice is cancelled.
- On the format side, the release notes also announce **EDI import/export in UBL format** (that specific format ships in its own UBL module, not in `account_edi` itself).

## Technical data model changes

**Removed**
- The `ir.attachment` extension disappears: the `edi_format_id` field on attachments is gone. EDI files are no longer identified as attachments.
- `hide_on_journal` on `account.edi.format` is removed.
- `account.move.edi_document_ids` is no longer a One2many to `ir.attachment`.

**Added**
- New model **`account.edi.document`** (`Electronic Document for an account.move`): `move_id`, `edi_format_id`, `attachment_id`, `state` (to_send / sent / to_cancel / cancelled), `error` (HTML), plus related `name` / `edi_format_name`. SQL constraint: one document per move per format.
- `account.move`: `edi_document_ids` (One2many to the new model), computed `edi_state`, `edi_error_count`, `edi_web_services_to_process`, `edi_show_cancel_button`; new methods `button_cancel_posted_moves()` and `action_process_edi_web_services()`.
- `account.journal.compatible_edi_ids` (computed), and `edi_format_ids` becomes a stored, computed, editable field filtered on `compatible_edi_ids`.
- `account.payment.action_process_edi_web_services()`; `account.move.line` now overrides `reconcile()` and `remove_move_reconcile()` to refresh payment EDI documents.

**Method signature changes on `account.edi.format`** — the old export hooks were replaced by:
- `_is_required_for_invoice(invoice)` / `_is_required_for_payment(payment)`
- `_needs_web_services()`, `_is_compatible_with_journal(journal)`, `_is_embedding_to_invoice_pdf_needed()`, `_support_batching()`
- `_post_invoice_edi(invoices, test_mode=False)`, `_cancel_invoice_edi(...)`, `_post_payment_edi(...)`, `_cancel_payment_edi(...)` — all returning `{record: {'attachment'/'error'}}` or `{record: {'success'/'error'}}`.

These are internal APIs: only relevant if you maintain a custom EDI format.

## How your habits should change

- **Journal configuration is automatic.** Compatible formats are pre-selected on each journal based on its type and company; you no longer pick from a static list.
- **You cannot deactivate a format on a journal while documents are still waiting** to be sent or cancelled — Odoo blocks it with an explicit message.
- **You cannot reset a posted invoice to draft once its EDI has been sent.** Use *Request EDI Cancellation*; the invoice is cancelled only after the cancellation succeeds.
- **Watch the error counter** on invoices and fix problems before sending reminders; nothing is retried blindly.
- **Reconciling or unreconciling a payment can regenerate its EDI documents**, so payment reconciliations are now an EDI-relevant action.
- Asynchronous formats need the scheduled action/CRON to run (or a manual trigger).

## What you gain by migrating

- Full traceability: every invoice and payment knows which EDI formats are required, which file was produced, and whether it was accepted.
- Real error reporting instead of silent failures — safer month-end closes.
- Payment-level EDI, needed in countries where payment data must be reported.
- Asynchronous web-service flows handled by the CRON, with batching to reduce API calls.
- A cleaner, more consistent data model (no more fake attachments), which makes custom formats and reporting easier to maintain.
- Access to the UBL import/export feature family introduced in 14.0.

