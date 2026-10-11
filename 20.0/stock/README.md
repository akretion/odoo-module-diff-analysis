# stock migration guide (19.0 -> 20.0)

This page summarises what changes in the Inventory (`stock`) addon between 19.0 and 20.0, for functional users and for a customer planning the upgrade.

## What's new for users

- **Batch, wave and cluster transfers are now built into Inventory.** Enabling the "Batch, Wave & Cluster Transfers" setting is enough; no separate app or bridge module is needed.
- **The allocation (reception) report is configured per operation type.** The global "Reception Report" setting disappears; each operation type decides whether the allocation report is shown and/or printed automatically at validation.
- **Suggested stock levels for reordering rules.** A new wizard rewrites the min/max basis and factor on several reordering rules at once and recomputes daily demand, minimum and maximum quantities from demand history.
- **Product replenishment:** the "Order" and "Order to max" buttons are consolidated into a single "Order" button. When ordering in advance, the quantity is max minus forecast.
- **Scrapping is now a stock move.** The Scrap menu opens a list of scrap operations, and a lot/serial number can be scrapped directly from its own form. Scrap reasons are kept, as tags.
- **Simplified returns**: the return wizard has been removed.
- **Inventory at a past date**: a date picker in the stock report, preserving filters.
- Stock aging report accessible from the Moves Analysis pivot view.
- Traceability report shows upstream and downstream lots/serials in a single report.
- Delivery methods and shipping connectors are grouped in one view instead of being split across menus.

## Technical data model changes

**Models**
- `stock.scrap` is removed; scrap operations are plain `stock.move` records.
- `stock.picking.batch` (with its wave/cluster models, wizards and views) moves from the `stock_picking_batch` module into `stock`.

**`stock.move`**
- Added: `is_scrap`, `scrap_reason_tag_ids`, `should_replenish_scrapped`.
- `scrap_id` removed, replaced by `is_scrap`; `product_uom` renamed `uom_id`; `reference` becomes editable; `lot_ids` is now filtered on the move's product.
- New `action_scrap`, `check_available_qty`, `_action_replenish`, `action_open_allocation_report`, `action_print_reception_report`; scrap-related branches of `action_see_move_scrap` dropped.

**`stock.move.line`**
- `scrap_id` removed, `is_scrap` (related) and `batch_id` (related) added; `product_uom_id` renamed `uom_id`; `action_revert_inventory` renamed `action_revert`.

**`stock.picking` / operation types**
- `move_ids` now excludes scrap moves; `button_scrap` replaced by `action_scrap`; `action_see_move_scrap` returns scrap moves.
- `show_operations` removed here, on `stock.move` and on operation types.
- `auto_show_reception_report` takes over from the removed `group_stock_reception_report` setting.

**`stock.lot`**: `product_uom_id` renamed `uom_id`; `is_scrap` added; new `action_scrap` and `action_open_scrap_moves`.

**Reordering rules**: `based_on` → `min_max_based_on`, `percent_factor` → `min_max_based_on_factor`, plus new period/demand helpers and the suggest wizard.

**Other**
- `virtual_available`, `incoming_qty` and `outgoing_qty` move from `stock` to the `product` module, allowing simplified stock tracking without the Inventory app.
- Delivery connector settings (DHL, UPS, FedEx, etc.) are removed from Settings and replaced by a unified delivery-methods action.
- Deleted dead code: `package_type_sequence_id`, unused quantity-computation methods.

## How your habits should change

- **Scrapping**: you no longer fill in a scrap order. Open Scrap, create the record (a draft stock move), then confirm it to post the quantity move. Scrap reasons are selected as tags on the move. Note that scrapping a kit is no longer possible.
- **Lot/serial numbers**: use the Scrap button on the lot form (and the "Is Scrapped" filter) instead of searching scrap orders.
- **Batch work**: turn on the Batch setting in Inventory settings; transfers previously handled by the separate Batch app behave the same, but wave creation and grouping are now native.
- **Reception report**: enable "Show Allocation" on each operation type rather than globally.
- **Reordering**: use the new suggestion wizard before adjusting min/max manually.
- **Integrations, Studio fields, exports and custom reports** that reference `product_uom`/`product_uom_id` must be updated to `uom_id`, and reordering-rule field names changed.
- Scrap references no longer live on a dedicated document, so filters saved on the old Scrap menu should be rebuilt on stock moves.

## What you gain by migrating

- One single scrap flow driven by stock moves: fewer objects, less duplicated data, and scrap traceability directly in move analysis.
