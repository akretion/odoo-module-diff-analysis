# stock_account migration guide (18.0 -> 19.0)

## What's new for users

Odoo 19 rewrites inventory valuation in `stock_account`. The official release notes summarize it as: "Inventory valuation has been simplified and new features have been added, including a new closing interface and support for transfer backdating."

In practice:
- Valuation modes are renamed: "Manual" becomes Periodic (at closing), "Automated" becomes Perpetual (at invoicing). Behavior follows the name: perpetual valuation is posted when you invoice or bill, not when you deliver or receive.
- Input and Output interim accounts are gone. In perpetual, the closing report shows the purchase/sales documents delivered but not yet invoiced so you can post the corresponding accruals. Full automation of accrual entries is still listed as remaining work by Odoo.
- A new closing interface drives periodic valuation: Odoo compares the balance of the valuation account with the computed value of the products and proposes the difference.
- Backdated transfers are supported; the errors they used to create are absorbed by the closing entry instead of replaying history.
- Manual value adjustments are recorded in a dedicated product value log instead of being deduced from a layer ledger.

## Technical data model changes

Removed
- Model `stock.valuation.layer`, plus its `stock_valuation_layer_ids` relations on `account.move`, `account.move.line`, `product.product`, `stock.move` and `stock.lot`.
- On `product.category`: `property_stock_account_input_categ_id`, `property_stock_account_output_categ_id`, `valuation_in_account_id`, `valuation_out_account_id`, `group_stock_accounting_automatic`.
- On `product.template`/`product.product`: computed `value_svl`, `quantity_svl`, `total_value` and computed `avg_cost`.
- `account.move.stock_move_id`, and the anglo-saxon interim reconciliation method.

Added / changed
- `account.move.stock_move_ids` (One2many) supersedes `stock_move_id`.
- New model `product.value` logging manual valuation updates.
- `account.account`: `account_stock_variation_id`, `account_stock_expense_id`.
- `product.template.property_price_difference_account_id` (perpetual difference between standard and bill price).
- `property_stock_journal` renamed to `stock_journal`; chart templates now write `stock_journal` and no longer push stock accounts as `ir.default`.
- `avg_cost` becomes a stored field (notably on lots) instead of computed.
- `account.move._post` now calls `line_ids._get_stock_moves()._set_value()`.
- Renames: `_stock_account_prepare_anglo_saxon_out_lines_vals` -> `_stock_account_prepare_realtime_out_lines_vals`; `_stock_account_get_anglo_saxon_price_unit` -> `_get_cogs_value`; `_eligible_for_cogs` -> `_eligible_for_stock_account`.
- COGS lines are generated whenever valuation is perpetual (no longer gated on anglo-saxon); the stock side hits the valuation account instead of Stock Interim Delivered. Value priority: manual value, then bill/invoice, then PO/SO, then standard price.
- Bill/invoice lines: `_compute_account_id` assigns the valuation account for perpetual products, or the stock variation account under anglo-saxon accounting.

## How your habits should change

- Stop checking the Stock Valuation Layers list: it no longer exists. Trace value from stock moves and the valuation/closing report.
- In perpetual, expect accounting entries at invoice/bill time, not at delivery. Deliveries only move quantities.
- Configure a single stock valuation account (plus variation/expense accounts) instead of input/output accounts.
- Run the closing report regularly: it is where you spot gaps (uninvoiced deliveries, rounding, backdated moves).
- With standard or average cost, COGS uses the product cost; under FIFO it uses the average price of the related moves.
- Anglo-saxon users no longer reconcile interim accounts; review your account setup before go-live.

## What you gain by migrating

- Far less bookkeeping noise: no layer ledger to reconcile, no per-delivery interim entries.
- One auditable closing workflow for both periodic and perpetual valuation, with explicit visibility of goods delivered not invoiced and bills to receive.
- Backdating becomes usable again: mistakes are corrected at closing instead of forcing history replays.
- Simpler configuration for new companies, and a valuation justified from recent documents rather than the full move history.
