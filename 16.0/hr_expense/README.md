# hr_expense migration guide (15.0 -> 16.0)

This guide covers what changes for the Community `hr_expense` app when moving from 15.0 to 16.0.

## What's new for users

- **Default expense journal**: the journal used to generate accounting entries can be configured per company (Expenses / Accounting settings). If defined, it is used automatically instead of "the first purchase journal found".
- **Taxes are now included in the price**: the taxes selectable on an expense are "included in price" taxes. The price you enter already contains the tax, and Odoo breaks it out into *Tax amount* and *Untaxed amount*.
- **Clearer amounts**: expense reports display Untaxed Amount, Taxes and Total; expense lines show the total in currency, the total in company currency and the conversion rate used.
- **Simplified UX**: menus and filters for Expense Officers were simplified, default expense categories are provided on fresh databases, and expenses can be created in bulk by dragging and dropping documents.
- **Accounting no longer drives the HR state**: resetting to draft, cancelling or reversing the journal entry no longer changes the state of the linked expense report. A new **Unpost** action reverses the entry and puts the report back to draft.

## Technical data model changes

- `hr.expense` now inherits `analytic.mixin`: `analytic_account_id` and `analytic_tag_ids` are **removed**, replaced by `analytic_distribution`. Prefill now comes from `account.analytic.distribution.model` (product, account, company).
- New fields on `hr.expense`: `product_description`, `amount_tax`, `amount_tax_company`, `untaxed_amount`, `currency_rate`, `unit_amount_display`, `product_has_tax`. Removed: `label_total_amount_company`.
- `tax_ids` label is now "Included taxes" and its domain is restricted to `price_include = True`; `product_id` ("Category") is renamed "Product"; `total_amount` is now "Total In Currency" and `total_amount_company` "Total". Unit price and totals are recomputed through the new `currency_rate`.
- `hr.expense.sheet`: new `expense_number`, `untaxed_amount` and `total_amount_taxes`; `_compute_amount` aggregates taxes; new `action_unpost` and `action_get_expense_view`.
- `account.move`: the `button_cancel` override is removed; new `expense_sheet_id` (One2many) and `action_open_expense_report`; `_payment_state_matters` moved to `account.move`.
- `account.move.line.reconcile` no longer updates expense/report states for reversed entries.
- `product.template`: removing supplier taxes when creating an expensable product moved from `create` to `default_get`.
- `res.company`: new `expense_journal_id`, also exposed in settings.
- Submitting a mix of "own account" and "company account" expenses now creates **two** reports; `_get_default_expense_sheet_values` returns a list. Validation text: "Expenses must have the same To Reimburse status."

## How your habits should change

- **Re-check taxed expenses**: previously a 100.00 price with 15% tax gave a 115.00 total; now 100.00 is the total, with 13.04 tax and 86.96 untaxed. Amounts imported or keyed with the old logic must be reviewed.
- **Change how you encode analytics**: select analytic accounts in the new distribution widget. Analytic tags no longer exist, and plans replace analytic groups.
- **Set the default expense journal** once per company if you don't want to pick it on every report.
- **Rename your vocabulary**: "Category" is now "Product" on expense lines.
- **Stop checking report states after accounting actions**: use the Unpost button when you want to reverse an entry and return the report to draft.
- **Zero-amount expenses can no longer be reported** (e.g. while waiting for OCR); an explicit error is raised.

## What you gain by migrating

- Correct tax accounting on expenses, with taxed, untaxed and tax amounts visible on lines and reports.
- Less confusion between accounting and expense workflow: accountants can work on journal entries without breaking employees' expense reports.
- One clean analytic mechanism shared with invoices and journal items, instead of the deprecated analytic tags.
- Smoother daily work for Expense Officers: default journal, simplified menus, bulk creation by drag and drop, and a one-click Unpost.
