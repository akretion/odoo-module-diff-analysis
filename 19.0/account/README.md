# account migration guide (18.0 -> 19.0)

This guide summarizes what functionally and technically changes in the `account` addon when moving from Odoo 18.0 to 19.0 (Community edition).

## What's new for users

- **Rebuilt bank reconciliation**: a simpler kanban-style view (one card per statement line, unfold for details), with keyboard shortcuts. Draft entries can now be reconciled, and the automatic moves they generate (currency exchange, cash basis) are created in draft alongside and confirmed when the entry is posted. You can fully reconcile or write off partially reconciled items.
- **Simplified reconciliation models**: the "invoice matching" and "write-off suggestion" rule types are gone — that logic is now built in. A model just triggers manually or automatically (new `Trigger` field) and can schedule a follow-up activity.
- **Reworked fiscal positions**: tax mappings are no longer entered on the fiscal position. Each tax declares which fiscal position it belongs to and which taxes it replaces. Taxes are filtered by default according to the fiscal position, while replaced taxes remain available via "Search More".
- **Tax tags**: the +/- signs have been removed from tax tags; inversions are handled directly on the report lines.
- **Light audit trail is now available by default for everyone**. An optional restrictive mode blocks deletion of posted journal items and of related attachments/logs (forced in some localizations, e.g. Germany).
- **Receipts merged with invoices/bills**: purchase receipts are always available (switch bill/receipt on the form); sale receipts are enabled in settings. Localizations may force a default fiscal position on purchase receipts (e.g. Belgium non-deductible).
- **Partial deductibility of bills**: set a "Deductibility" percentage on each bill line; the private part is posted automatically on a dedicated account configured per purchase journal.
- **Cleaner screens**: journal creation wizard from the dashboard, revamped journal and reconciliation-model forms, invoice line display settings stored separately for invoices and bills, account descriptions, account default taxes no longer applied on miscellaneous entries, improved duplicate-bill detection, dedicated Debit Note button, deferred miscellaneous entries with start/end dates.
- **Less clutter**: journal "Allowed accounts" restriction and blocking partner/product invoice warnings are removed; OFX/CSV/CAMT bank statement import settings are gone (those modules remain auto-installed).

## Technical data model changes

**Added**
- `account.document.import.mixin` (abstract), inherited by `account.move`: new import/EDI framework. One attachment may now generate **several** documents (batched EDI files); mail-alias attachments are grouped before records are created. `_extend_with_attachments` now takes `files_data`; the `no_new_invoice` context key is replaced by `disable_attachment_import`; the decoder hook changed (`_get_edi_decoder` now returns a decoder + priority).
- `account.move.line.deductible_amount`; `reconciled_lines_ids` (computed many2many with inverse triggering reconciliation); new display types `non_deductible_product`, `non_deductible_product_total`, `non_deductible_tax`.
- `account.journal.non_deductible_account_id`; `res.company.domestic_fiscal_position_id`, `restrictive_audit_trail`, `force_restrictive_audit_trail`, `account_purchase_receipt_fiscal_position_id`.

**Removed / renamed**
- Models: `account.fiscal.position.tax`, `account.reconcile.model.partner.mapping`, `bank.rec.widget` (+ lines).
- Fields: `account.move.line.tax_key`, `compute_all_tax`, `compute_all_tax_dirty`, `total_tax_factor`, `tax_tag_invert`, `child_ids`; `account.full.reconcile.exchange_move_id`; `account.account.tag.tax_negate`; partner `invoice_warn`/`invoice_warn_msg`; journal `account_control_ids`, `alias_id` (now `alias_name`); company `check_account_audit_trail` (renamed `restrictive_audit_trail`) and the receipt default-tax fields; many reconciliation-model fields (`rule_type`, `auto_reconcile` → `trigger`, `matching_order`, `counterpart_type`, tolerance fields, `number_entries`, ...).
- `account.report.filter_fiscal_position` → `allow_foreign_vat`; `account.report.external.value.foreign_vat_fiscal_position_id` removed; `account_opening_date` no longer defaulted.

**Behaviour changes**
- Tax engine rewritten: "round globally" now rounds per tax and per line before dispatch, giving consistent totals (including price-included taxes and analytic distributions). Tax totals keys renamed (e.g. `amount_total` → `total_amount_currency`).
- `account.move.line.parent_id` is no longer stored.
- Cash-basis transition accounts must be reconcilable (new constraint).
- Foreign VAT fiscal positions are now unique per country.

