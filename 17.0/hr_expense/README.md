# hr_expense migration guide (16.0 -> 17.0)

## What's new for users

**Accounting flow revamp.** A report paid by the employee now generates a vendor bill; a report paid by the company generates a payment instead of a purchase receipt. The payment method used for company-paid expenses can now be selected.

**One payment per expense.** Company-paid reports now generate as many payments as there are expense lines, which makes bank reconciliation much easier.

**Default category.** A default expense category can be set for automatically generated expenses.

**Forced amount in company currency.** For expenses in a foreign currency, employees can enter the amount actually spent in company currency, independently of Odoo's exchange rates.

**Clearer statuses.** Expense and report stages were harmonised and documented with tooltips on the expense dashboard.

(OCR/AI extraction, Documents "Create Expense" and payroll reimbursement are separate or Enterprise apps; they are not part of Community hr_expense.)

## Technical data model changes

Removed fields: `hr.expense.is_refused`, `hr.expense.amount_residual`, `journal_displayed_id`, `expense_alias_prefix`, `use_mailgateway`.

Renamed: `use_mailgateway` -> `hr_expense_use_mailgateway`, `expense_alias_prefix` -> `hr_expense_alias_prefix`.

Report/entry link: `hr.expense.sheet.account_move_id` (Many2one) becomes `account_move_ids` (One2many); `account.move.expense_sheet_id` becomes a Many2one.

New fields on `hr.expense.sheet`: `approval_state`, `cannot_approve_reason`, `employee_journal_id`, `payment_method_line_id`, `selectable_payment_method_line_ids`, `nb_account_move`, `company_currency_id`. `state`, `payment_state` and `amount_residual` are now computed from the linked entries, and report totals are expressed in company currency.

Configuration: `res.company.company_expense_allowed_payment_method_line_ids` (payment method lines) replaces `company_expense_journal_id`.

`hr.expense` gains `check_company=True` on product, taxes, account and journals (multi-company/branch support); `account.move.line.expense_id` is now copied.

New extension of `account.payment` (open the report, creation message, unlink guards).

No method signature change was detected, but these methods were renamed: `_get_taxes` -> `_get_taxes_results`, `_prepare_move_line_vals` -> `_prepare_move_lines_vals`, `_prepare_bill_vals` -> `_prepare_bills_vals`, `_prepare_payment_vals` -> `_prepare_payments_vals`, `approve_expense_sheets` -> `action_approve_expense_sheets`, `refuse_sheet` -> `action_refuse_expense_sheets`, `reset_expense_sheets` -> `action_reset_expense_sheets`, `paid_expense_sheets` -> `set_to_paid`. Customisations calling them must be adapted.

## How your habits should change

- Employee-paid reports are now vendor bills (not receipts); company-paid ones post one payment per expense, so you reconcile several entries.
- For company-paid expenses you select a Payment Method rather than a Bank Journal.
- A single expense line can no longer be refused: refusal applies to the whole report.
- Resetting an approved or paid report reverses the posted entries instead of deleting them.
- Approval errors now explain the reason (your own expense, another department...).
- Foreign-currency expenses: the exchange rate field lets you force the company-currency amount.

## What you gain by migrating

- Cleaner accounting: proper vendor bills per employee and payments that match real spending, with far less manual reconciliation work.
- Accounting and Expense stay synchronised on the report (payment status, amount due, journal entries).
- Reliable email-alias configuration for expenses (no crashes, alias recreated if deleted).
- Branch/subsidiary ready: shared products, taxes, accounts and journals across companies.
- Consistent, better documented statuses: less confusion for employees and approvers alike.
- A supported path forward: 16.0 is out of mainstream support, and 17.0 brings the reworked expense accounting flow your accountants expect.
