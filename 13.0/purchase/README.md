# purchase migration guide (12.0 -> 13.0)

In 13.0 the `purchase` addon no longer relies on `account.invoice`: vendor bills are now journal entries, like every other accounting document. This guide lists what changes for Purchase users coming from 12.0 (Community edition).

## What's new for users

**Vendor bills are journal entries.** The 13.0 accounting refactor merges customer invoices, vendor bills and journal entries into a single model. For purchase:

- Saving a bill as a draft now creates a draft accounting entry: drafts can be reported and budgeted.
- Opening an entry from the purchase journal always opens the correct vendor-bill form, whatever menu you came from.
- Editing is more flexible, because the bill and its journal entry are the same document.
- Purchase orders support sections and notes on their lines.
- A new dashboard is available to analyze purchases.

One small regression: grouping several bill lines for the same product during validation no longer happens.

## Technical data model changes

Models removed and merged into `account.move` / `account.move.line`: `account.invoice`, `account.invoice.line`, `account.invoice.tax`, `account.voucher`, `account.voucher.line` (module `account_voucher` removed). In `purchase`, `account_invoice.py` becomes `account_move.py`, with `AccountInvoice` -> `AccountMove` and `AccountInvoiceLine` -> `AccountMoveLine`.

Fields on account.move (new, non-stored):
- `purchase_id` (purchase.order) - the "Purchase Order" auto-complete field.
- `purchase_vendor_bill_id` (purchase.bill.union) - replaces 12.0 `vendor_bill_purchase_id` ("Auto-Complete").

account.move.line:
- `purchase_line_id` is kept but is no longer read-only.
- the related `purchase_id` field (related to `purchase_line_id.order_id`) is removed.

purchase.order / purchase.order.line:
- `invoice_ids` now targets `account.move` (was `account.invoice`); `_compute_invoice` depends on `order_line.invoice_lines.move_id`.
- `invoice_lines` on order lines now targets `account.move.line`; `_compute_qty_invoiced` depends on `invoice_lines.move_id.state` and uses `product_uom_id` (was `uom_id`).
- The "Bills" button opens `account.action_move_in_invoice_type` (was `account.action_vendor_bill_template`) with `default_type` (was `type`) and view `account.view_move_form`.

Renamed fields (bill): `name`->`invoice_payment_ref`, `number`->`name`, `reference`->`ref`, `comment`->`narration`, `user_id`->`invoice_user_id`, `origin`->`invoice_origin`, `date_invoice`->`invoice_date`, `date_due`->`invoice_date_due`, `sent`->`invoice_sent`, `payment_term_id`->`invoice_payment_term_id`, `vendor_bill_id`->`invoice_vendor_bill_id`, `residual`->`amount_residual`; line: `invoice_id`->`move_id`, `uom_id`->`product_uom_id`, `invoice_line_tax_ids`->`tax_ids`, `account_analytic_id`->`analytic_account_id`.

Methods: `_onchange_bill_purchase_order` and `purchase_order_change` are merged into `_onchange_purchase_auto_complete`; `_prepare
