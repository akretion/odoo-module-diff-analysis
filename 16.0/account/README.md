# account migration guide (15.0 -> 16.0)

## What's new for users

**Bank statements are now optional.** Bank transactions stand alone: you encode statement lines directly and only group them into a statement for control purposes. Statements can carry an end date, an ending balance and a scanned copy; Odoo warns when lines have no statement (gap) and when the ending balance does not match. Lines show a cumulative balance and can be reordered by drag and drop within the same date.

**Payment terms got a new screen.** Term lines are editable on the fly with new fields: months, days, end of month, days after end of month, plus a live example preview. The new "Display terms on invoice" option details due dates and amounts on the invoice. Cash (early payment) discounts move to the payment terms, and the Register Payment wizard suggests the reduced amount.

**Analytics.** The analytic tag widget on invoice lines is replaced by an analytic distribution widget (plans and accounts). Analytic plans replace groups, distribution models replace analytic defaults, and analytics can be edited in mass from list views.

**Multi-currency reconciliation.** Exchange difference entries are now created at each partial payment, not only on full reconciliation, and a foreign-currency line reconciled against a company-currency line is reconciled in the foreign currency. The invoice payment widget highlights exchange differences and shows amounts in both currencies.

Other Community changes: a bill's currency conversion uses the invoice/bill date; partner bank accounts are logged in the chatter and automatically added ones must be validated; journal sequence gap alerts on the dashboard; simplified account types; Storno accounting setting; drag and drop of files on dashboard cards.

Note: the reworked report engine and the reconciliation widget described in the release notes rely on Enterprise apps. Report *definitions* now live in `account`, but their computation stays in Enterprise.

## Technical data model changes

Removed models: `account.account.type`, `account.analytic.tag`, `account.analytic.default`, `account.cashbox.line`, `account.bank.statement.cashbox`, `account.bank.statement.closebalance`, `account.tax.report` (+ line).
Added models: `account.report`, `account.report.line`, `account.report.expression`, `account.report.column`, `account.analytic.distribution.model`, `account.analytic.applicability`.

- `account.account`: `user_type_id` / `internal_type` replaced by a single `account_type` selection; `internal_group` and `include_initial_balance` moved onto the account. On `account.journal`, `type_control_ids` is removed and `default_account_type` becomes a Char.
- `account.move.line`: `analytic_account_id` + `analytic_tag_ids` replaced by `analytic_distribution`; `account_internal_type` → `account_type`; `is_rounding_line` / `exclude_from_invoice_tab` merged into `display_type`; `sequence` is computed and stored.
- `account.move`: `tax_totals_json` → `tax_totals` (dict); `date`, `journal_id`, `company_id`, `name` are computed-stored; `invoice_date_due`, `invoice_payment_term_id`, `partner_shipping_id`, `partner_bank_id`, `fiscal_position_id` are stored computed fields; `statement_line_ids` and `payment_ids` added.
- `account.payment.term.line`: `option`, `day_of_the_month` and `sequence` removed; `months`, `end_month`, `days_after` added.
- Method signatures: `_compute_taxes()` and `_prepare_tax_totals()` lost the `early_payment_term` argument. Onchange methods are renamed with a leading underscore (`onchange_journal_id` → `_onchange_journal_id`). Invoice amounts are computed instead of copied by onchange, and synchronization with journal items happens in `create`/`write`.

## How your habits should change

- Forget the statement closing wizard and cashboxes: encode statement lines, then optionally group them into a statement.
- Rebuild payment terms with months/days/end of month; keep exactly one Balance line.
- Use the analytic distribution widget instead of tags and analytic account fields; convert analytic defaults into distribution models.
- Set "Allowed accounts" on journals instead of "Allowed account types".
- Expect exchange difference entries on each partial payment; no manual write-off needed.
- Any customization using `user_type_id`, `internal_type`, analytic tags/defaults, cashbox models or `res_bank` must be adapted.

## What you gain by migrating

- Speed: creating a 500-line invoice drops from about 2 minutes to about 4 seconds, and the test suite runs faster.
- More consistent invoices: totals, taxes and payment terms are computed declaratively, so EDI/OCR/intercompany flows no longer need the form emulator and produce fewer inconsistent states.
