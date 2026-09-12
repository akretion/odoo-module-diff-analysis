# stock migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes contain no section dedicated to this addon, so everything below is derived from the 20.0 code changes themselves.

- **Scrapping is now a normal stock move.** The dedicated *Scrap* order model (`stock.scrap`) is gone. The Scrap menu shows a list of stock moves flagged as scrap; a record created there is a draft move that becomes done when confirmed. Scrap reason tags and the "Replenish Quantities" option are still available, now on the move itself.
- **Scrap straight from a lot/serial number.** The lot/serial form has a *Scrap* button that opens a pre-filled scrap move, plus a button listing that lot's scrap moves.
- **Kits can no longer be scrapped** (limitation of the move-based approach).
- **Batch, Wave & Cluster Transfers are now part of Inventory.** The separate `stock_picking_batch` app disappears; the feature is switched on with a simple setting instead of installing an extra module.
- **Allocation / Reception Report becomes an option per operation type.** Instead of one global setting, each operation type carries its own "Show Allocation" and auto-print options, so receipts and internal transfers can behave differently.
- **Reordering rules get a suggest wizard** to recompute in bulk the demand period, the factor, the daily demand and the min/max quantities.

## Technical data model changes

- Removed model `stock.scrap`; added `stock.scrap.reason.tag`. The batch/wave/cluster models and views moved from `stock_picking_batch` into `stock`.
- `stock.move`: `scrap_id` removed; `is_scrap`, `scrap_reason_tag_ids` and `should_replenish_scrapped` added; `reference` is now editable; `lot_ids` is domain-filtered by product.
- `stock.move.line`: `scrap_id` removed; `is_scrap` and `batch_id` (related) added.
- `stock.lot`: new `is_scrap` flag.
- `res.company`: computed `scrap_location_id` and `default_stock_location_id`; a scrap move now defaults from the company stock location to the scrap location.
- `stock.picking`: `move_ids` excludes scrap moves; `has_scrap_move` and `action_see_move_scrap` now key on `is_scrap`.
- `stock.picking.type`: `auto_show_reception_report` now gates allocation reports; the global `group_stock_reception_report` is removed. The label "Operation Category" is renamed "Type of Operation".
- Renames: `stock.move.product_uom` → `uom_id`; `stock.move.line.product_uom_id` → `uom_id`; `stock.lot.product_uom_id` → `uom_id`; orderpoint `based_on` → `min_max_based_on` and `percent_factor` → `min_max_based_on_factor`.
- Settings: `module_stock_picking_batch` replaced by `group_stock_picking_batch`; `group_stock_reception_report` and the per-carrier `module_delivery_*` toggles are removed from Inventory (connectors are reached from a single Delivery Methods view).
- `virtual_available`, `incoming_qty` and `outgoing_qty` moved to the base `product` module (no impact when Inventory is installed).
- Methods: `stock.move.action_scrap()`, `_action_scrap()`, `check_available_qty()`, `_action_replenish()`, `_prepare_scrap_move_vals()`, `action_print_reception_report()`; `stock.lot.action_scrap()` and `action_open_scrap_moves()`; `stock.picking.button_scrap()` → `action_scrap()`; `stock.move.line.action_revert_inventory()` → `action_revert()`; batch/wave helpers added on `stock.move.line`. Dead code removed (`show_operations`).

## How your habits should change

- Scrap operations are no longer separate documents: look for them in the Scrap menu, in the move list, or from the picking's *Scraps* button. Reports or filters based on `stock.scrap` must be rebuilt on scrap moves.
- If you scrapped kits, define an alternative process (scrap the components or use inventory adjustments).
- Batch/wave behaviour is now a setting, not an installed app; check that it is enabled after migration.
- Review each operation type to set "Show Allocation" and the automatic report/label printing, since the previous global setting no longer applies.
- Custom code, exports or server actions using `product_uom` on moves, or `product_uom_id` on move lines/lots, must use `uom_id`; orderpoint fields renamed likewise.
- Customizations on shipping connectors should use the new unified Delivery Methods view.

## What you gain by migrating

A simpler, more consistent stock engine: one model for both transfers and scrapping, fewer modules to install and maintain for batching and waves, allocation reports tuned per operation type, a bulk wizard for reordering rules, and a unified unit-of-measure naming that makes customizations and integrations easier to write and less error-prone.
