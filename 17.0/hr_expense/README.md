# hr_expense migration guide (16.0 -> 17.0)

## What's new for users

- **Reworked accounting flow.** An expense report paid by the employee now generates a **vendor bill**; a report paid by the company generates a **payment** instead of a purchase receipt. Entries are easier to find and follow standard payment states.
- **Payment method for company-paid expenses.** You can now pick the payment method used when the company pays, instead of just a bank journal.
- **One payment per expense.** Company-paid reports generate as many payments/journal entries as there are expenses in the report, which makes bank reconciliation much easier when expenses are reimbursed separately.
- **Forced amount in company currency.** For expenses in a foreign currency, the employee can manually enter the amount actually spent in company currency, independently of Odoo's exchange rate.
- **Clearer statuses.** The pipeline terminology is now consistent between expenses and expense reports (To Submit / Submitted / Approved / Posted / Done), "to submit" and "to report" are clarified, and the expense dashboard has tooltips.
- **Improved expense report PDF**, with the receipts attached.
- **Default category** can be configured for automatically generated expenses (Settings).

Enterprise-only extras (OCR/Extract, payslip reimbursement) are out of scope of this Community guide.

## Technical data model changes

Large refactor of `hr.expense` / `hr.expense.sheet`, plus a workflow change on how moves are generated.

**Renamed fields** (affects exports, saved filters, RPC, custom reports):

| 16.0 | 17.0 |
|---|---|
| `unit_amount` | `price_unit` |
| `amount_tax` (foreign currency) | `tax_amount_currency` |
| `amount_tax_company` | `tax_amount` |
| `total_amount` (foreign currency) | `total_amount_currency` |
| `total_amount_company` | `total_amount` |
| `untaxed_amount` | `untaxed_amount_currency` |
| `attachment_number` | `nb_attachment` |
| `same_currency` | `is_multiple_currency` |

**Removed fields:** `unit_amount_display`, `reference` (Bill Reference) and its editability flag, `sample` (moved to `hr_expense_extract`), `address_id` on the report, `bank_journal_id`, `journal_displayed_id`, `is_refused`, `amount_residual` on the expense, and `sheet_is_editable` (merged into `is_editable`).

**Workflow and accounting fields:**
- `sheet.account_move_id` → `account_move_ids` (One2many) plus a `nb_account_move` counter: one report can produce several moves/payments.
- `sheet.state` is now computed from `approval_state` and `payment_state`; new `approval_state` (Submitted/Approved/Refused) and `cannot_approve_reason`.
- Journal selection reworked: `employee_journal_id` (paid by employee) + `payment_method_line_id`; `journal_id` is computed. On the company, `company_expense_journal_id` is replaced by `company_expense_allowed_payment_method_line_ids`.
- `amount_residual` on the report is computed from all linked moves, including partial and reversed ones.
- `company_id` is read-only on the expense; `product_uom_id` is computed from the product.
- Multi-company/branch support: company checks added on products, taxes, accounts and journals.
- `account.move.expense_sheet_id` is now a Many2one, and deleting only part of the entries or payments of a report is blocked.
- Settings fields renamed: `expense_alias_prefix` → `hr_expense_alias_prefix`, `use_mailgateway` → `hr_expense_use_mailgateway`.

## How your habits should change

- Employee-paid reports are now vendor bills: find them in the Vendor Bills list, not in receipts.
- A company-paid report can show several payments/entries: open the report and use its button to list them.
- Choose the payment method on the report (company-paid) rather than a bank journal.
- Rebuild saved filters, exports and custom reports that referenced renamed or removed fields.
- Individual expenses can no longer be "refused": only the whole report is.
- Check the Expenses settings: alias options were renamed and now recreate the alias if it was deleted.

## What you gain by migrating

- **Correct, auditable accounting**: standard vendor bills and payments instead of receipts, with consistent payment states and no sequence holes.
- **Faster bank reconciliation**: one payment per expense for company-paid reports.
- **Accurate foreign-currency expenses** thanks to the forced company-currency amount.
- **A cleaner workflow**: single "To Submit" entry point, clearer statuses, and explicit reasons when a report cannot be approved.
- **Multi-company/branch ready**: accounts, taxes and journals are properly filtered by company.
- **Less legacy**: dead fields, OCR leftovers and deprecated code removed, so views and exports are more consistent.
