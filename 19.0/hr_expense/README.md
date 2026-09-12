# hr_expense migration guide (18.0 -> 19.0)

## What's new for users

**Expense reports are gone.** Odoo 19 aligns Expenses with the way people actually work: most "reports" only ever contained a single expense. Each expense is now a standalone record you submit, approve, post and pay on its own. When you do need to process several at once, select them in the list view and use the submit / approve / post actions in bulk. For employee-paid expenses, posting several at once still generates **one single vendor bill per employee**.

**Simpler approval.** Submitting an expense now also approves it automatically when the employee has no expense manager configured — no pointless validation step for small teams.

**A clearer lifecycle, per expense.** The statuses are now: Draft → Submitted → Approved → Posted → In Payment → Paid (plus Refused). The old "To Report" / "To Submit" / "Done" wording disappears. Approval is tracked separately with an Approval State and an Approval Date stored on the expense itself.

**The accounting trail is visible on the expense.** Each expense shows the journal entry it generated, and payment method fields are set directly on the expense for company-paid expenses.

## Technical data model changes

**Models**
- `hr.expense.sheet` is **removed** (~49 fields and relations dropped); its remaining logic moves into `hr.expense`.
- The `ir.attachment` extension of the module is removed.

**`hr.expense`**
- Added: `account_move_id`, `department_id`, `manager_id`, `approval_state`, `approval_date`, `total_amount`, `untaxed_amount`, `amount_residual`, `journal_id`, `payment_method_line_id`, `selectable_payment_method_line_ids`, `can_reset`, `can_approve`, and `former_sheet_id` (integer, keeps the former report id).
- Removed: `sheet_id`, `approved_by`, `approved_on`, `accounting_date` (was related to the sheet) and the report-level totals.
- `state` selection rewritten: `draft`, `submitted`, `approved`, `posted`, `in_payment`, `paid`, `refused`. `reported` and `done` no longer exist. New constraint: only draft expenses may have a zero total.
- `is_editable` / `can_approve` / `can_reset` are now computed from user groups and the manager hierarchy instead of the report.

**`account.move`**
- `expense_sheet_id` → `expense_ids` (One2many on `account_move_id`) plus `nb_expenses`. `show_commercial_partner_warning` removed.
- `action_open_expense_report` → `action_open_expense`.
- New constraint: each company-paid expense must have its own dedicated journal entry.

**`account.payment`**: `expense_sheet_id` → `expense_ids` (related to `move_id.expense_ids`); the "linked to an expense report" guard now speaks about expenses.

**`hr.department`**: `expense_sheets_to_approve_count` → `expenses_to_approve_count`, counting expenses in state `submitted`.

**`hr.employee`**: the expense manager now defaults to the parent manager's user without requiring the approver group, and the selection domain follows the parent hierarchy.

**`res.company` / `res.config.settings`**: `expense_outstanding_account_id` is removed. The outstanding account comes from the payment method line, otherwise from the chart of accounts' default outstanding account (created automatically if missing). Nothing to set in Settings any more.

## How your habits should change

- Stop creating expense reports: capture the expense, submit it, done.
- Bulk work now happens through list-view selection, not through a report form.
- One company-paid expense = one journal entry. Employee-paid expenses posted together still produce one bill per employee, so keep grouping them when you want a single reimbursement document.
- Replace any filter, saved view, automated action or spreadsheet that referenced `hr.expense.sheet` or `sheet_id` with expense-level criteria (employee, department, status).
- Rename your old status filters ("To Report", "Done" no longer exist).
- Check your chart of accounts before go-live: the default outstanding accounts must exist (they are created on demand) and be active.
- Review who is an expense manager: with none configured, submission equals approval.

## What you gain by migrating

Fewer clicks per expense, no artificial report to maintain, batch processing that matches real usage, a single unambiguous status per expense, a direct link to the generated journal entry, approval rules driven by the HR hierarchy, and one less accounting setting to configure. Existing expense data is migrated, with the former report reference preserved on each expense.

*Note: the release notes also mention disallowed-expense percentage changes and physical expense cards; those belong to other / Enterprise addons and are not part of this Community module's changes.*
