# hr_expense migration guide (18.0 -> 19.0)

## What's new for users

**Expense reports are gone.** Expense reports were removed because most reports contained a single expense, and forcing a report added unnecessary steps. You now create one expense and submit it directly.

**Batch submission, approval and posting from the list view.** Select several expenses in the Expenses list and submit, approve or post them together: the old report-by-report flow is replaced by standard multi-select actions.

**Consolidated reimbursements.** For employee-paid ("to reimburse") expenses, posting several expenses at once generates a single vendor bill per employee.

The 19.0 release notes for Expenses are short. The physical expense card features (Stripe / Mastercard Issuing) and the Belgian disallowed-expense percentage change belong to Enterprise or localization addons, not to this Community addon. Everything else below comes from the actual code changes.

## Technical data model changes

Model removed
* `hr.expense.sheet` (including its `ir.attachment` override) is deleted; its remaining logic moved to `hr.expense`.

`hr.expense`
* Removed: `sheet_id`, `approved_by`, `approved_on`, `accounting_date`.
* Added: `department_id`, `manager_id` (stored computes), `approval_state`, `approval_date`, `account_move_id`, `journal_id`, `payment_method_line_id`, `selectable_payment_method_line_ids`, `untaxed_amount`, `amount_residual`, `can_reset`, `can_approve`, and `former_sheet_id` (integer, keeps the old report grouping for data migration).
* `state` selection reworked: `draft`, `submitted`, `approved`, `posted`, `in_payment`, `paid`, `refused`. The old `draft` / `reported` / `done` values are gone; `approval_state` tracks submitted / approved / refused separately.
* New constraints: a total of 0 is allowed only in draft; one expense per payment; each company-paid expense needs its own journal entry.
* Behaviour: submitting an expense also approves it when no expense manager is set for the employee.

`account.move`
* `expense_sheet_id` → `expense_ids` (One2many), `nb_expenses` added, `show_commercial_partner_warning` removed, `action_open_expense_report` → `action_open_expense`.
* A move can only hold several expenses when none is paid by the company.

`account.payment`: `expense_sheet_id` → `expense_ids`; `action_open_expense_report` → `action_open_expense`.

`account.move.line`: filters on `expense_id.payment_mode` instead of the sheet.

`hr.department`: `expense_sheets_to_approve_count` → `expenses_to_approve_count` (counts submitted expenses).

`hr.employee`: `expense_manager_id` is now taken from the employee's parent/manager without requiring the expense group.

`res.company` / `res.config.settings`: `expense_outstanding_account_id` removed; the outstanding account now comes from the chart of accounts (created automatically if missing), while a per-payment-method account is still honoured.

## How your habits should change

* Stop creating reports: create expenses and submit them individually or by multi-selection.
* Use multi-select plus the Actions menu to submit, approve and post in one go.
* Company-paid expenses: one expense = one journal entry, so group only employee-paid expenses (they merge into a single bill per employee).
* Review employee managers, since approval routing no longer requires the expense group.
* Accounting: configure outstanding accounts in the chart of accounts rather than in the Expenses settings.

## What you gain by migrating

Fewer clicks end to end, with no report to create or forget; faster approval for employees who have no expense manager (auto-approval on submit); batch handling of many expenses; consolidated per-employee bills for reimbursements; a leaner data model with no intermediate report object; and less configuration, since the outstanding account follows the standard chart of accounts. The upgrade path keeps the legacy report grouping in `former_sheet_id`, so historical data stays readable.
