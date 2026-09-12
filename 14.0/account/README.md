# account migration guide (13.0 -> 14.0)

## What's new for users

- **Payments go through outstanding accounts.** Customer and vendor payments are posted on "Outstanding Receipts/Payments" accounts configured on the journal; the money reaches the bank account only at bank reconciliation. Manual journal entries can be reconciled on these accounts too.
- **Better payment tracking.** Payment status (printed, cancelled, date of the action) is now traced, and an invoice/bill can be marked as partially paid when the amount received is lower than the balance.
- **Editable document numbering.** Choose the numbering sequence directly when posting your first invoice or bill, instead of going through technical sequence settings.
- **"To check" flag** on invoices/bills to follow up on documents that need review.
- **Smarter reconciliation models:** they can match on customer/vendor and also look into the note and reference, not only the label.
- **Chart of accounts:** the account hierarchy is now built automatically from the account code.
- **Cash rounding:** separate accounts for rounding gains and losses.
- **Bank accounts:** SEPA BIC is no longer mandatory.

Note: the bank reconciliation widget, aged partner balance, ledger/financial reports, unrealized gains & losses report and multi tax reports belong to Odoo Enterprise and are not part of this Community addon.

## Technical data model changes

Removed or renamed:
- `account.journal.post_at` removed (the Payment Validation / Bank Reconciliation posting choice is gone).
- Journal `default_debit_account_id` / `default_credit_account_id` replaced by a single `default_account_id`.
- Journal `sequence_id`, `refund_sequence_id`, `sequence_number_next`, `refund_sequence_number_next` removed. `account.move.name` becomes a stored, editable computed field (with `highest_name`), plus `sequence_override_regex` on journals.
- `account.move.line.tag_ids` renamed `tax_tag_ids` (SQL tables unchanged).
- `account.fiscal.year` model removed from Community (moved to Enterprise); the `account_fiscal_country_*` config parameters are replaced by `account_tax_fiscal_country_id` on `res.company`.
- `account.bank.statement.line` loses its direct links to statements, accounts and partner bank accounts; the reconciliation model `second_*` write-off fields are dropped.

Added:
- Journal: `payment_debit_account_id`, `payment_credit_account_id`, `suspense_account_id`.
- New models `account.reconcile.model.line` (and its template), `account.group.template`; `account.partial.reconcile` and `account.full.reconcile` moved to dedicated files, with per-currency amounts stored on each partial.
- `account.group`: `code_prefix_start`/`code_prefix_end`, `company_id`; `account.account.group_id` is now computed from the account code.
- Company level: `income_currency_exchange_account_id`, `expense_currency_exchange_account_id` and the cash-basis base account.

## How your habits should change

- Do not look for "Next number" on journals anymore: set or edit the entry number on the document itself.
- Reconciliation models: add as many write-off lines as needed instead of the old fixed "second line".
- One "Default Account" now covers both debit and credit sides of a journal.
- Bank statements no longer use a reconciliation threshold; statements lines create entries immediately on the suspense account.
- An invoice is "paid" only when reconciled with an entry on the journal's liquidity account; with outstanding accounts, an intermediate "in payment" state is expected.
- Account groups: create accounts and let Odoo assign the group; adjust only for exceptions.

## What you gain by migrating

- A clearer, auditable payment lifecycle: outstanding accounts make the gap between payment and bank statement explicit.
- Less configuration and fewer technical screens: numbering, default accounts, account groups and reconciliation models are all simpler and more flexible.
- More reliable multi-currency reconciliation, thanks to per-currency amounts stored on each partial reconciliation.
- Better day-to-day visibility with the "To check" flag, payment status tracking and automatic account hierarchy.
