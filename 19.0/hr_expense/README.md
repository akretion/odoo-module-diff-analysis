# hr_expense migration guide (18.0 -> 19.0)

## What's new for users

- **Expense reports are removed.** Most reports contained a single expense, so the extra grouping step has been dropped.
- **Batch actions from the list view.** Submit, approve, post and pay several expenses at once by selecting several lines.
- **Grouped accounting for employee-paid expenses.** Posting several employee-paid expenses at once generates a single accounting document per employee.
- **One expense, one payment.** Company-paid expenses are settled by their own payment entry, making the expense/payment link explicit.
- **Faster attachment handling** on expenses with many receipts.

The 19.0 Expenses notes also announce physical expense cards (Mastercard / Stripe Issuing). That is Enterprise-only and is not part of the Community addon described here.

## Technical data model changes

- `hr.expense.sheet` is removed, with its ~70 methods (approval, posting, payment, activity update). All workflow logic now lives on `hr.expense`.
- `hr.expense` gains `action_submit/approve/refuse/reset/post/pay`, `action_open_account_move`, `action_open_split_expense`, and computes `can_approve`, `can_reset`, `payment_method_line_id`, `selectable_payment_method_line_ids`. Payment becomes one-to-one (`_check_o2o_payment`, `_create_company_paid_moves`).
- `res.company` / `res.config.settings`: `expense_outstanding_account_id` is removed. The outstanding account now comes from the chart template and is created automatically if missing; an archived account raises a warning with a link to it.
- `hr.department`: `_compute_expense_sheets_to_approve` becomes `_compute_expenses_to_approve_count`.
- `account.move`: `action_open_expense`, `_check_expense_ids`, `_compute_nb_expenses` replace the report-oriented helpers.
- `hr.employee`: `_search_filter_for_expense` moves from `hr.employee.base` to `hr.employee` / `hr.employee.public` (contracts merged into the employee model).
- Accounting engine: new tax computation hooks on `account.tax` / `account.move.line`, and `_compute_outstanding_account_id` on `account.payment`. Internal, but custom modules using the old hooks must be adapted.
- `ir.attachment` create/unlink overrides are replaced by `_get_attachment_by_record` (performance).

## How your habits should change

- Forget "expense reports": no sheet to create, name, fill and submit — you act directly on expenses.
- To send several expenses for approval, select them in the list and use Submit; approvers approve from the same list.
- The approval reason and "can I approve" flags are shown per expense, not per report.
- Expenses are now analysed by status, employee and dates on the expense list rather than through report records.
- In Settings, the expense Outstanding Account field is gone. Check your expense journal and payment methods; Odoo uses (and creates if needed) the default outstanding account of your chart of accounts.
- Ask your integrator for a data migration plan: historical reports and their approval trail must be reviewed.

## What you gain by migrating

- A simpler daily flow: one object, fewer clicks, no report to build before submitting.
- Batch submit/approve/post/pay on many expenses in a single action.
- A clear one-to-one link between expense, journal entry and payment, simplifying reconciliation and audit.
- Less setup: the outstanding account no longer has to be configured manually.
- Better performance on expenses with many attachments.
- A codebase aligned with 19.0 accounting, a safer base for future upgrades and custom developments.
