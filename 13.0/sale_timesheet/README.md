# sale_timesheet migration guide (12.0 -> 13.0)

## What's new for users

- **Timesheets visible on the invoice portal.** Timesheets related to an invoice can now be consulted on the portal: your customer opens the invoice and sees the time that was invoiced.
- This is the only item of the 13.0 release-notes extract that really concerns `sale_timesheet` in Community.
- The other timesheet entries in the release notes (Start/Stop timer on tasks, timesheet grid, mobile timesheet grid, Field Service worksheets/records) come from other modules (`hr_timesheet`), from the Enterprise edition or from the mobile app. They are **not** delivered by `sale_timesheet` Community, so do not expect them from this migration.
- The Sales and Accounting sections of the release notes bring no new button to this addon. The visible change for you is the new accounting engine described below.

## Technical data model changes

Source: diff of the addon between 12.0 and 13.0 (the "accounting-pocalypse" refactoring).

Models removed / replaced
- `account.invoice` and `account.invoice.line` no longer exist: they are merged into `account.move` and `account.move.line`.
- `sale_timesheet` no longer inherits them. File `models/account_invoice.py` is renamed `models/account_move.py`: class `AccountInvoice` becomes `AccountMove` (`_inherit = "account.move"`), class `AccountInvoiceLine` becomes `AccountMoveLine` (`_inherit = "account.move.line"`).

Field change
- `account.analytic.line.timesheet_invoice_id` is now a `Many2one` to **`account.move`** instead of `account.invoice` (label "Invoice" kept, still read-only and not copied). The timesheets invoiced from a sale order now point to the journal entry that represents the invoice.

Method signature change
- `create()` on the invoice-line model moves from `@api.model create(self, values)` to `@api.model_create_multi create(self, vals_list)`: it accepts a list of values and returns a recordset (batch creation). The business logic is unchanged: when a line is added to a draft customer invoice (`type = 'out_invoice'`, `state = 'draft'`) whose sale line is invoiced on delivered quantity, the matching timesheets get their `timesheet_invoice_id`.

Other renames to watch in custom code
- Project overview searches `account.move` instead of `account.invoice`; action `account.action_invoice_tree1` becomes `account.action_move_out_invoice_type`.
- Invoice line fields: `invoice_id` -> `move_id`, `uom_id` -> `product_uom_id`, `invoice_line_tax_ids` -> `tax_ids`, `account_analytic_id` -> `analytic_account_id`.
- Invoice / journal entry fields: `number` -> `name`, `date_invoice` -> `invoice_date`, `date_due` -> `invoice_date_due`, `origin` -> `invoice_origin`, `amount` -> `amount_total`; `state` is complemented by `invoice_payment_state`.

## How your habits should change

- "Customer Invoices" is now a filtered view of journal entries. The invoice generated from your timesheets *is* a journal entry; validating it posts it.
- A draft invoice now exists as a draft accounting entry, so it can already be used in reporting.
- The automatic timesheet-to-invoice link still happens when the invoice is created from the sale order: no extra step for the user.
- Any custom report, export or field based on `account.invoice`, on the target of `timesheet_invoice_id`, or on the renamed fields listed above must be reviewed during the project.

## What you gain by migrating

- One single model for customer invoices, vendor bills and journal entries: less duplication, easier edition, changes reflected on both sides.
- Customers can consult the invoiced timesheets on the portal, which reduces questions and disputes about timesheet-based invoices.
- Batch creation of invoice lines, which improves comfort and performance when invoicing many timesheets at once.
- You stay on a supported Odoo version, with a code base that no longer carries the old invoice model.
