# account migration guide (18.0 -> 19.0)

What changes in the `account` addon when moving from Odoo 18.0 to 19.0.

## What's new for users

- **Bank reconciliation redesigned**: a simpler view replaces the old widget, automatic models recognize transactions better, and keyboard shortcuts are available.
- **Fiscal positions without tax mappings**: each tax declares on its own form which fiscal position it applies to and which taxes it replaces.
- **Partial deductibility**: on bills and purchase receipts, set a "Professional percentage" per line; the private part is booked on the journal's dedicated account.
- **Receipts merged with invoices and bills**: switch type on the vendor bill form; sale receipts are enabled in settings.
- **Light audit trail enabled by default**, with an optional restrictive mode that localizations can force.
- **Cleaner forms and menus**: revamped journal and reconciliation model views, a new journal creation wizard, a dashboard link to draft miscellaneous entries.
- **Default taxes on accounts** now apply only to invoices and bills, never to miscellaneous entries; income accounts are suggested first on customer invoices, expenses on bills.
- **Easier daily work**: duplicate-bill warnings, PDF preview of documents linked to bank transactions, follow-up exclusions, better recipient bank account selection, report annotations in the chatter.

## Technical data model changes

- New abstract model `account.document.import.mixin`, inherited by `account.move`: `_to_files_data`/`_unwrap_attachments` build an intermediate `files_data` format, `_extend_with_attachments` takes `files_data`, `_get_edi_decoder` is replaced by `_decode_attachment`, and one file can generate several records.
- `account.move.line`: added `deductible_amount` and `reconciled_lines_ids`; `parent_id` is no longer stored, `child_ids` removed; `tax_key`, `compute_all_tax`, `compute_all_tax_dirty`, `total_tax_factor` and `tax_tag_invert` removed; new non-deductible display types. Tax totals keys were renamed (`amount_total` -> `total_amount_currency`) and global rounding is recomputed tax by tax.
- `account.reconcile.model`: `auto_reconcile` becomes `trigger` (Manual/Automated), `to_check` becomes `next_activity_type_id`. Removed: `rule_type`, `matching_order`, `match_note`, `match_transaction_details`, `match_text_location_*`, `match_same_currency`, `match_partner*`, `past_months_limit`, `decimal_separator`, `partner_mapping_line_ids`, `allow_payment_tolerance`, `payment_tolerance_*`, `counterpart_type`, `journal_id`, `number_entries`; added `can_be_proposed`, `mapped_partner_id` and, on lines, `partner_id`.
- Models removed: `account.reconcile.model.partner.mapping`, `account.fiscal.position.tax`. `account.fiscal.position.tax_ids` is now a many2many to `account.tax`; `account.tax` gains `fiscal_position_ids`, `original_tax_ids` and `is_domestic`.
- `res.company`: `check_account_audit_trail` renamed `restrictive_audit_trail`, plus new `force_restrict
