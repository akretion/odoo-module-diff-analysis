# account migration guide (13.0 -> 14.0)

## What's new for users

- **Automatic account hierarchy.** The chart of accounts now groups itself from the account codes: you no longer have to fill in the parent group or the group on each account (still overridable for exceptions).
- **Choose your numbering.** You can pick the numbering sequence you want when posting your first customer invoice or vendor bill, and the entry number is now directly editable.
- **Reconciliation models with several lines.** Write-off/suggestion lines are no longer limited to two.
- **Better audit trail** on the entry number and on tax-grid tags.
- **Payments through outstanding accounts**: customer and vendor payments are posted to outstanding accounts and only reach the bank account when the bank statement is imported.

Note: several accounting items mentioned in the 14.0 release notes are Enterprise-only and are **not** included in the Community edition (fiscal years, aged partner balance, reconciliation widget).

## Technical data model changes

**account.group / account.account** — `code_prefix` is replaced by `code_prefix_start` + `code_prefix_end` (equal length enforced, no overlap between groups of the same granularity); `company_id` becomes required and `parent_id` is read-only. `account.account.group_id` becomes a stored computed field and the old `onchange_code` heuristic disappears. New model `account.group.template`; groups are generated from the chart template.

**account.reconcile.model / .line** — new models `account.reconcile.model.line` and `account.reconcile.model.line.template`. All `second_*` fields, `has_second_line` and `amount_from_label_regex` are removed and replaced by `line_ids` (One2many) with `amount_type`/`amount_string`, `tax_ids` and `analytic_tag_ids`.

**account.move / account.journal** — `name` is now stored, editable and computed. New fields `posted_before`, `highest_name`, `show_name_warning`, and `sequence_override_regex` on the journal. Removed from the journal: `sequence_id`, `refund_sequence_id`, `sequence_number_next`, `refund_sequence_number_next`; from the move: `invoice_sequence_number_next(_prefix)`. New abstract model `sequence.mixin`, also used by bank statements.

**account.move.line** — `tag_ids` is renamed `tax_tag_ids` (same table and columns, so existing reports keep working).

**Signatures and removals** — `_get_taxes_move_lines_dict` is now an instance method (`ensure_one`); `_get_write_off_move_lines_dict` gained a `residual_balance` argument. `account.fiscal.year` and `res.company.compute_fiscalyear_dates()` are gone from Community.

## How your habits should change

- Check your account codes first: groups and their parents are now derived from the codes, not from what you typed manually.
- Forget the journal "Next Number" fields; numbering is handled on the entry itself.
- Because numbers are editable, the prefix matters: renaming mid-period can make the next number reuse an existing prefix. A per-journal regex can enforce a strict format (advisor only).
- Rebuild your reconcile models as line lists instead of first/second line.
- Replace any customisation or report using `tag_ids` on move lines with `tax_tag_ids`.
- Fiscal-year records are no longer available in Community.

## What you gain by migrating

- Less manual setup, and an account hierarchy that stays consistent.
- User-editable numbering, without going through `ir.sequence` settings.
- Reconciliation models that go beyond two lines and are much easier to maintain.
- A supported, modern base with a clear upgrade path prepared by Akretion.
