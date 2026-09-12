# stock_account migration guide (12.0 -> 13.0)

## What's new for users

The big functional change in Odoo 13.0 is how stock is valued. The official release notes state it plainly: *"The valuation of the stock is now represented by valuation layers. It allows users to have a clear view of how the stock is valued."* Instead of reading valuation through the older per-move amounts (and the "Valuation at date" screens built on top of them), every stock move now produces one clear **Stock Valuation Layer** that you can list, filter and audit. Each layer shows the quantity and the value that entered or left your stock.

Configuration is also safer: Odoo now raises a warning during product category setup when the Stock Received/Delivered accounts are the same as the Stock Valuation account. Landed costs can additionally be applied to products valued in AVCO.

The rest of the 13.0 release notes covers accounting, localizations and Enterprise-only applications and does not change this addon.

## Technical data model changes

**Products – removed fields**
- `product.template`: `property_valuation`, `valuation` (computed + inverse), `property_cost_method`, `cost_method` (computed + inverse), `property_stock_account_input`, `property_stock_account_output`.
- `product.product`: `stock_value`, `qty_at_date`, `stock_value_currency_id`, `stock_fifo_real_time_aml_ids`, `stock_fifo_manual_move_ids`.

**Products – new behaviour**
- `cost_method` and `valuation` are now read-only *related* fields pointing to `categ_id.property_cost_method` / `categ_id.property_valuation`. Costing method and valuation type are configured on `product.category` only.
- `_get_product_accounts()` reads stock input/output accounts from the category; per-product account fields are gone.
- Changing the standard price no longer goes through `do_change_standard_price()`.

**Stock moves – removed fields**
- `value`, `remaining_qty`, `remaining_value` on `stock.move` are replaced by `stock.valuation.layer` (`stock_valuation_layer_ids`) and by the product fields `value_svl` / `quantity_svl`.

**Removed methods / objects**
- The `procurement.group` inheritance and its `_run_scheduler_tasks` hook that triggered the FIFO vacuum.
- `_run_fifo`, `_run_valuation`, `_fifo_vacuum`, `_run_fifo_vacuum`, `_compute_stock_value`, `_sum_remaining_values`, `_get_fifo_candidates_in_move(_with_company)`, `action_valuation_at_date_details`, `action_open_product_moves`, and the `_get_in_*` / `_get_all_*` domain helpers.

**stock.move.line**: `create()` / `write()` no longer depend on the `svl` context key to write valuation data.

No method signature changes were reported by the diff tool for this addon.

## How your habits should change

- Set costing method and inventory valuation on the **product category**, not on the product. If you used per-product overrides in 12.0, recreate them as dedicated categories.
- Stock input/output and stock valuation accounts are taken from the category; the per-product account fields no longer exist.
- Follow valuation through **Stock Valuation Layers** rather than the old per-move value fields. This is now the way to explain a product's value and quantity.
- The "Valuation at date" action is gone from the product form; use the valuation layer list with date filters instead.
- FIFO vacuum corrections are no longer launched by the scheduler hook of this module.
- Plan a cost-method switch carefully: the automatic standard-price recomputation that this module used to perform when moving to AVCO/standard is no longer part of it, so check your stock value after switching.

## What you gain by migrating

- **One auditable source of truth**: valuation layers attached to moves replace scattered legacy fields.
- **Fewer configuration mistakes**: valuation is centralized on categories, with a safeguard warning on account misconfiguration.
- **A cleaner data model**: obsolete fields and methods removed, aligned with the 13.0 stock and accounting engine.
- **Better alignment with 13.0 inventory features**, including landed costs on AVCO-valued products and improved inventory reporting.
- **Future-proofing**: this is the valuation model used by all later Odoo versions, easing future upgrades and support.
