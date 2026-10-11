# hr_expense migration guide (19.0 -> 20.0)

Scope: the **Expenses** app (`hr_expense`) plus its accounting bridge, when moving from Odoo 19.0 to Odoo 20.0.

## What's new for users
- **Consolidated expenses report** — select several expenses in the list view and print them together in a single report, instead of printing them one by one.
- **Expense limits per job position** — maximum allowed amounts can now be defined per job position on expense products. Managers can cap an expense at the authorized limit before granting final approval.
- **The standalone "Employee Expenses" menu is gone (Accounting side)** — approved expenses now generate draft bills in the default expense journal, so accountants can filter, review and post them alongside standard vendor bills.

The release notes for 20.0 say nothing else specific to `hr_expense`.

## Technical data model changes
Taken from the 19.0 → 20.0 code diff of the addon.
- The model `hr.expense.product.job.position.limit` ("Expense Product Limit by Job Position") has been **moved out of `product_product.py` into its own file** `models/hr_expense_product_job_position_limit.py`. This is a code-organization change only, no behavioral impact.
- On that model, one field was replaced:
  - **removed**: `currency_id` (Many2one related to `product_id.currency_id`, readonly)
  - **added**: `company_currency_id` (Many2one related to `product_id.cost_currency_id`)
- `limit_amount` (Monetary) now declares `currency_field='company_currency_id'` instead of `currency_id`.
- Unchanged: `sequence`, `product_id`, `job_ids` (empty = limit for all job positions), the "only one generic limit per product" constraint, and the `_order`.
- No method signature changes, and no other model or field added/removed in this addon.

Net effect: limit amounts are now read and displayed in the **company/cost currency** of the product, matching the currency in which the limit is configured.

## How your habits should change
- **Check your existing limit records.** The stored `limit_amount` values are *not* converted; only the currency they are interpreted and displayed in has changed. If a limit was entered while the screen showed a different currency, review it after migration so the cap stays correct.
- **Stop printing expenses one by one.** Use selection + the consolidated report to produce a single document for several expenses.
- **Look for approved expenses in Accounting / Bills**, not in a dedicated "Employee Expenses" menu: they now appear as draft bills in the expense journal, where you post them like other vendor documents.
- When you define a new product limit by job position, remember the amount is a company-currency amount, not a sales-currency amount.

## What you gain by migrating
- **Consistent currency handling for expense limits**: the limit is shown in the same currency it is configured in, removing misleading figures for products bought or sold in a foreign currency.
- **A single accounting flow for expenses**: approved expenses land as draft bills in the expense journal, so they can be filtered, reviewed and posted next to other vendor documents, making reconciliation and month-end follow-up easier.
- **Less paperwork**: consolidated expense printing produces one report for many expenses.
- **Better spend control**: per-job-position limits are visible to approvers and enforced before final approval.
- **Low migration risk**: for this addon the change is limited to one currency field on the limit model plus a file move; the rest of the expense data model is untouched.
