# hr_expense migration guide (18.0 -> 19.0)

## What's new for users

**Expense reports are gone.** In 18.0 you had to create an expense report (`hr.expense.sheet`) to group expenses before submitting, approving and posting them. In 19.0 the report object no longer exists: an expense is now its own document, which matches the common usage where a report usually contained a single expense.

To keep the batch workflow, select several expenses in the list view and use the action buttons to submit, approve or post them all at once.

- Employee-paid (to reimburse) expenses selected together still generate **a single vendor bill per employee**.
- Company-paid (paid by company) expenses must be posted individually: each one gets its own dedicated journal entry.

**Approval is simplified.** A new approval state (Submitted / Approved / Refused) lives on the expense itself, with an approval date. When an employee has no expense manager, submitting an expense also approves it automatically, so no unnecessary validation step blocks the flow.

**Disallowed expenses** now use an allowed percentage instead of a disallowed percentage, so deductibility above 100% (e.g. Belgium) can be expressed; the report shows both amounts in that case (report availability depends on your Accounting apps).

Note: physical expense cards (Mastercard / Stripe Issuing) mentioned in the 19.0 release notes rely on Enterprise-only modules and are not part of a Community deployment.

## Technical data model changes

Removed model: `hr.expense.sheet`.

- `hr.expense`: removed `sheet_id`, `approved_by`, `approved_on`, `accounting_date`. Added `account_move_id`, `journal_id` (related to the payment method line), `payment_method_line_id` with `selectable_payment_method_line_ids`, `amount_residual`, `untaxed_amount`, `department_id`, `manager_id`, `approval_state`, `approval_date`, `can_reset`, `can_approve`, and `former_sheet_id` (integer preserving the old report grouping during migration).
- `hr.expense.state` reworked: `draft, submitted, approved, posted, in_payment, paid, refused` (`reported` and `done` are gone).
- `account.move`: `expense_sheet_id` replaced by an `expense_ids` One2many plus `nb_expenses`; `action_open_expense_report` becomes `action_open_expense`; a new constraint forbids several company-paid expenses on one entry; the "all entries must be deleted together" guard is removed.
- `account.payment`: `expense_sheet_id` becomes `expense_ids`; `action_open_expense_report` becomes `action_open_expense`; the same deletion guard is removed.
- `hr.department`: `expense_sheets_to_approve_count` becomes `expenses_to_approve_count` (counts submitted expenses).
- `hr.employee`: the expense manager is now derived from the parent hierarchy without requiring the expense approver group.
- `res.company` / `res.config.settings`: `expense_outstanding_account_id` is removed; the outstanding account now comes from the payment method's payment account or the chart of accounts default (created automatically if missing).

## How your habits should change

- Stop creating and validating expense reports: create expenses, then select them in the list view.
- Use multi-selection to submit, approve or post in batch, and check the Journal Entry smart button on the expense.
- Company-paid expenses: one expense = one entry. Expect separate postings instead of a single grouped entry.
- Remove the outstanding account setting from Accounting settings; if the default account is archived, activate it or the flow will warn you.
- Since approval can be automatic, make sure expense managers are configured for employees who still need a real validation.

## What you gain by migrating

- Fewer clicks: no report to create for a single expense.
- Consolidated posting for employee reimbursements, individual accuracy for company-paid expenses.
- Clearer statuses (draft, submitted, approved, posted, paid) tracked directly on the expense.
- One less configuration field to maintain for company-paid expenses.
- A supported upgrade path: history is preserved and old reports are collapsed into expenses.
