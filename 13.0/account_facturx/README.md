# account_facturx migration guide (12.0 -> 13.0)

## What's new for users
Odoo 13.0 merges the customer invoice, vendor bill and journal entry models into a single object (`account.move`). The 13.0 release notes sum it up as: *the models of customer invoice, vendor bill and journal entry have been merged, allowing more flexibility in the edition of those documents*.

For Factur-X nothing new is added: importing a Factur-X / ZUGFeRD PDF still creates a customer invoice or vendor bill, and exporting still generates the Factur-X XML attached to the PDF. But the document you land on is now a journal entry that behaves like an invoice, and a draft invoice is a real draft accounting entry. The other accounting novelties listed in the 13.0 notes (mass invoice import, original vendor bill printing, etc.) are not part of this community addon.

## Technical data model changes
- The addon no longer inherits `account.invoice`: `models/account_invoice.py` became `models/account_move.py`, and the model class `AccountInvoice` (`_inherit = 'account.invoice'`) became `AccountMove` (`_inherit = 'account.move'`). This is the only breaking model change recorded (1 removed, 0 added).
- Fields read/written on import use their new `account.move` names: `reference` → `ref`, `name` → `invoice_payment_ref`, `comment` → `narration`, `date_invoice` → `invoice_date`, `date_due` → `invoice_date_due`, `invoice_line_tax_ids` → `tax_ids`.
- `ir_actions_report._post_pdf()` now checks `self.model == 'account.move'` and `invoice.is_sale_document()` instead of `'account.invoice'` and `type in ('out_invoice', 'out_refund')`.
- Import requires context information: `_import_facturx_invoice()` reads `default_journal_id` or `default_type` and raises a UserError ("No information about the journal or the type of invoice is passed") when neither is available. The old `_default_journal()` guessing is gone.
- Refund detection was rewritten: TypeCode `381`, or a negative `GrandTotalAmount` with TypeCode `380`, switches the target to `out_refund`/`in_refund`; the type is set on the move before the form is filled.
- The old `date_due` readonly workaround (clearing the payment term first) was removed.
- `message_post()` now re-raises `except_orm`, so business errors during import are no longer logged as "malformed PDF" and silently ignored.
- No public method signature of the addon changed. Related core methods were renamed (`action_account_invoice_payment` → `action_invoice_register_payment`, `_run_post_draft_to_post` → `_autopost_draft_entries`).

## How your habits should change
- Invoices are journal entries now: the same document is reachable from Accounting > Customers > Invoices and from the Journal Entries list. Adding a line to an invoice adds a journal item.
- Customer/vendor receipts (vouchers) disappear; use a receipt-type journal entry.
- To credit an invoice, use the reversal wizard (`account.move.reversal`) instead of the removed refund object.
- Importing a Factur-X file from the vendor bill or invoice form works as before; any automation that calls the import directly must pass the journal or the document type.
- Because a draft invoice is now a draft entry, drafts can appear in reporting and budgets; review filters that relied on `account.invoice`.

## What you gain by migrating
- One single accounting object: invoices, bills and entries can no longer diverge, and editing a document updates its accounting entry (and the reverse) immediately.
- A maintained code base: account_facturx 13.0 is aligned with the standard Odoo 13 accounting engine, so future upgrades, customizations and localizations build on a supported foundation instead of a legacy duplicate model.
- Your Factur-X/ZUGFeRD round trip is preserved: PDF export with embedded XML, and import of supplier Factur-X files, with clearer error reporting on malformed files.
- Draft invoices usable directly in budgets and reporting.
