# stock_account migration guide (18.0 -> 19.0)

## What's new for users

The official 19.0 release notes cover this area with a single line, under **Inventory**: *"Inventory valuation has been simplified and new features have been added, including a new closing interface and support for transfer backdating."* Everything else below comes from the addon's own changes.

- **Valuation modes renamed.** "Manual" / "Automated" become **"Periodic (at closing)"** and **"Perpetual (at invoicing)"**. Product forms and filters using the old labels must be updated.
- **A closing interface.** A dedicated closing step compares the inventory value computed from documents with the balance of the valuation account and suggests the correcting entry. It also surfaces errors such as backdated moves and rounding differences.
- **Backdated transfers are supported**, and are regularised by the closing entry instead of rewriting history.
- **A closing report shows goods delivered but not invoiced and bills to receive**, so you can book the corresponding accruals.
- **No more input/output interim accounts.** The stock input and output accounts (and their reconciliation) are gone. Valuation now relies on a single stock valuation account, plus a stock variation/expense account.
- **New "Price Difference Account"** on the product category (perpetual valuation), holding the gap between the standard price and the bill price.
- **Value priority is explicit**: manual value > bill/invoice > purchase/sales order > standard price. Manual updates are logged in a new `product.value` object.

## Technical data model changes

- **`stock.valuation.layer` is removed.** All related fields disappear from `stock.move`, `product.product`, `product.template`, `stock.lot`, `account.move` and `account.move.line`. Value is now stored on the **stock move**.
- New model **`product.value`** records manual valuation updates.
- `account.move`: `stock_move_id` becomes the One2many `stock_move_ids` (inverse `stock.move.account_move_id`).
- `account.move.line`: new file, `stock_valuation_layer_ids` removed; `cogs_origin_id` kept; `_eligible_for_cogs` becomes `_eligible_for_stock_account`; `_stock_account_get_anglo_saxon_price_unit` becomes `_get_cogs_value`; new `_get_stock_moves`; the `_get_exchange_journal` / `_get_exchange_account` overrides are gone.
- `account.move`: `_stock_account_prepare_anglo_saxon_out_lines_vals` becomes `_stock_account_prepare_realtime_out_lines_vals`; `_stock_account_anglo_saxon_reconcile_valuation` and `_stock_account_get_last_step_stock_moves` are removed (replaced by `_get_related_stock_moves`); `_post` no longer reconciles COGS lines but recomputes move values.
- COGS journal items now debit the **stock valuation account** and credit the expense/variation account, instead of the old stock output/input interim accounts.
- `account.account`: new `account_stock_variation_id` and `account_stock_expense_id`.
- `product.template`: new `property_price_difference_account_id`; removed `property_stock_account_input_categ_id`, `property_stock_account_output_categ_id`, `valuation_in_account_id`, `valuation_out_account_id`, `group_stock_accounting_automatic`; `property_stock_journal` renamed `stock_journal`.
- `product.product`: `avg_cost` and `total_value` are now **stored** fields; `value_svl` and `quantity_svl` are removed. `stock.lot.avg_cost` is likewise a stored Monetary field.
- `product.template.write()` no longer empties and replenishes stock with valuation entries when the category, cost method or lot valuation changes.
- The chart template no longer pushes category stock accounts through `ir.default` (`_post_load_data` removed).

## How your habits should change

- **Post the invoice or bill** to generate cost of goods sold and valuation entries; deliveries and receipts no longer move valuation.
- **Stop configuring input/output accounts.** Configure the valuation account, the stock journal and, for perpetual, the price difference account.
- **Run the closing routine at period end** instead of relying on interim account reconciliation. Review the suggested difference entry, backdated moves, and the delivered-not-invoiced / billed-not-received sections before validating accruals.
- Changing a cost method or category no longer writes automatic stock-out/stock-in entries: the value is recomputed from documents, so verify the result through the closing report.
- **Any customization or report built on stock valuation layers (`value_svl`, `quantity_svl`, SVL records, anglo-saxon reconciliation methods) must be rewritten.** Plan a technical audit before migrating.

## What you gain by migrating

- **Much simpler stock accounting**: no valuation-layer table, no interim input/output accounts, no COGS reconciliation run.
