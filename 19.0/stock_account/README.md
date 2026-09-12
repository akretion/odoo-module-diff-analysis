# stock_account migration guide (18.0 -> 19.0)

`stock_account` is the module that links Inventory and Accounting. In 19.0 it has been largely rewritten: the "Stock Valuation Layer" ledger is gone, and inventory value is now derived from accounting documents.

## What's new for users

The 19.0 release notes summarise this change in the Inventory section: *"Inventory valuation has been simplified and new features have been added, including a new closing interface and support for transfer backdating."* Translated for daily use, that means:

- **Valuation is now document-driven.** A move's value comes from its bill/invoice, otherwise from the purchase/sales order, otherwise from the product's standard price.
- **A new closing interface** replaces the endless valuation-layer history. You close a period, Odoo compares the valuation account balance with the computed value of your stock and proposes the correcting entries.
- **Backdated transfers are supported.** Instead of rejecting or replaying history, the next closing absorbs the difference.
- **Goods delivered but not invoiced (and bills to receive) are shown in the closing report**, so you can post the accruals yourself.
- **Perpetual valuation posts COGS at invoicing**, not at delivery/receipt anymore.
- **Manual value corrections** can be logged by the user, without a full accounting justification.

## Technical data model changes

**Removed model:** `stock.valuation.layer` (deleted entirely, along with `stock_valuation_layer_ids`, `stock_valuation_layer_id`, `value_svl`, `quantity_svl` everywhere — product, lot, move, invoice line). **Added model:** `product.value` (manual value logging).

- `account.move`: `stock_move_id` → `stock_move_ids` (One2many); SVL relation removed; posting now sets the value on related stock moves; `_stock_account_prepare_anglo_saxon_out_lines_vals` → `_stock_account_prepare_realtime_out_lines_vals`.
- `account.move.line`: moved to its own file; COGS now use the **Stock Valuation** account instead of the former "stock output" interim account; `_eligible_for_cogs` → `_eligible_for_stock_account`; `_stock_account_get_anglo_saxon_price_unit` → `_get_cogs_value`; new `_get_stock_moves`; `_stock_account_anglo_saxon_reconcile_valuation` and the interim-account reconciliation logic were removed.
- `product.template`: `valuation` is now `periodic` ("Periodic (at closing)") / `real_time` ("Perpetual (at invoicing)"); `avg_cost` and `total_value` are stored instead of computed; new `property_price_difference_account_id`; `property_stock_account_input_categ_id`, `property_stock_account_output_categ_id`, `valuation_in_account_id`, `valuation_out_account_id` and `group_stock_accounting_automatic` removed; the `write()` that emptied/refilled stock when changing category or lot valuation was removed.
- `product.category`: `property_stock_journal` → `stock_journal`; input/output account properties dropped.
- `stock.lot`: SVL relation removed; `avg_cost` becomes a stored field.
- `account.account`: gains `account_stock_variation_id` and `account_stock_expense_id`.

## How your habits should change

- Review your product categories: you now choose **Periodic** or **Perpetual**, not "Manual"/"Automated".
- Stop configuring stock **input** and **output** accounts. Keep one Stock Valuation account; define a stock variation account and, for perpetual valuation, a price difference account.
- Do not expect a journal entry per stock move anymore. Perpetual postings happen at invoice/bill time; periodic values are posted at closing.
- Run an inventory closing each period rather than reading a valuation-layer history. Use the report to create accruals for received-not-billed goods.
- Value at a given date is obtained with a date context; only the last documents matter, not the full FIFO history.
- Since values are justified from bills, invoices and orders, keeping those documents complete and timely is now the main driver of valuation accuracy.

## What you gain by migrating

- Far less accounting noise: fewer journal entries, no interim input/output accounts to reconcile.
- One consistent valuation shared between Inventory and Accounting, easy to justify to an auditor.
- A closing workflow that handles backdating, rounding and late documents instead of failing.
- Clear visibility of accruals (received not invoiced, billed not received) in the closing report.
- A simpler configuration surface for product categories, and manual value adjustments when a real-world case needs one.
