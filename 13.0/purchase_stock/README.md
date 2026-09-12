# purchase_stock migration guide (12.0 -> 13.0)

## What's new for users

The 13.0 release notes cover Purchasing and Inventory as a whole, but only a few items really touch `purchase_stock`, the bridge module between purchase orders, receipts and vendor bills. Purchase-app novelties (purchase order sections and notes, batch variant creation from an attribute grid, the purchase dashboard) ship in the `purchase` module itself, not here.

What does matter for this module:

- **Vendor bills and journal entries are now one document.** The Accounting Engine merge means a vendor bill *is* a journal entry. Validating a bill posts it, and draft bills are immediately available in reporting and budgets.
- **Stock valuation is now held in valuation layers.** Value is tracked per stock move instead of globally, and the price-difference lines that `purchase_stock` adds to vendor bills are computed from these layers, so the difference reflects the real receipt cost (returns and exchange-rate cases included).

That is essentially all the release notes say about this addon; the rest of its behaviour follows from those two changes.

## Technical data model changes

Taken from the 12.0 → 13.0 diff of the addon:

- `models/account_invoice.py` no longer defines a model inheriting `account.invoice`. The old `AccountInvoice` class is removed and replaced by `AccountMove`, which inherits `account.move`. `account.invoice` no longer exists in 13.0.
- Removed methods: `invoice_line_move_line_get()` and `_anglo_saxon_purchase_move_lines()`. They are replaced by a single `_stock_account_prepare_anglo_saxon_in_lines_vals()`, which returns a list of dictionaries ready for `account.move.line.create()`.
- New `post()` override on `account.move`: the extra price-difference journal items for vendor bills (Anglo-Saxon accounting, real-time valued products) are now created when the bill is posted, instead of being injected while the invoice's move lines were prepared.
- `_get_last_step_stock_moves()` is renamed `_stock_account_get_last_step_stock_moves()` (override coming from `stock_account`).
- Field renames the code now relies on: `invoice_id` → `move_id`, `uom_id` → `product_uom_id`, `invoice_line_tax_ids` → `tax_ids`, `account_analytic_id` → `analytic_account_id`. New technical flags `display_type`, `exclude_from_invoice_tab` and `is_anglo_saxon_line` are used to keep those generated lines out of the invoice layout.
- `purchase.py`: the "quantities billed are lower than received" activity now points to `account.model_account_move` instead of `account.model_account_invoice`.
- `stock.py`: `_get_related_invoices()` now returns only vendor bills with `state == 'posted'`, where 12.0 kept every bill not in draft or cancelled.

## How your habits should change

- "Validate" a vendor bill is now "Post" the related journal entry; editing a bill edits that entry, and vice versa.
- Registering a payment on a bill uses the renamed `action_invoice_register_payment()` (previously `action_account_invoice_payment()`).
- Draft bills are already visible in reporting and budgets — you no longer have to post them just to see the amounts.
- Price-difference lines only appear once the bill is posted, so check a *posted* bill before investigating a valuation gap.
- Refunds and reversals go through `account.move.reversal`; the old `account.invoice.refund` wizard no longer exists.
- The option to group identical product lines together when validating an invoice has been dropped.
- Bills that are not yet posted are no longer returned as "related invoices" of a stock move; only posted ones count.

## What you gain by migrating

- **One document for bills and entries.** Less duplication, fewer inconsistencies between what you encode and what you report, and draft entries usable in budgets and analyses.
- **More accurate purchase valuation.** Because price-difference lines are built from stock valuation layers at posting time, vendor bills stay aligned with actual receipt costs, including returns and foreign-currency purchases.
- **Simpler, more readable technical base.** A single, well-named helper replaces the old move-line generation hooks, which makes future customisations of purchase valuation cheaper to maintain.
- **Cleaner related-document handling.** Stock moves now only point to posted bills, avoiding noise from draft or cancelled documents.

If you run Anglo-Saxon accounting with real-time valuation on purchased goods, this is the migration where it pays off to re-test your vendor bill workflow end to end.
