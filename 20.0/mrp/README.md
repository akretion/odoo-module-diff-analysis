# mrp migration guide (19.0 -> 20.0)

Migration guide for the Manufacturing (`mrp`) addon, Odoo 19.0 → 20.0, Community edition. No official release notes covered this addon: the points below come from the actual 20.0 code changes.

## What's new for users

- Work order planning: the hover popover warnings become two filterable flags, *Has planning issues* and *Has conflicts* (conflicts now also count in-progress work orders). The work order list colours late dates red and today orange, kanban cards show units, and you can search by manufacturing order references.
- Scrapping: the `stock.scrap` model disappears but the feature stays — a scrap is now a stock move flagged as scrap. You can scrap a lot/serial number from its form, record scrap reason tags and ask to replenish scrapped goods. Kits can no longer be scrapped.
- Unbuilding: one unbuild order can now reverse several serial numbers. Lot-tracked products still take one lot, with a button to clear the selection. The small unbuild dialog is replaced by the standard unbuild form.
- BoM maintenance: from a product's "used in BoMs" list you can multi-edit BoM lines and replace a component in several BoMs at once.
- Cleaner BoM forms: product documents are no longer managed from the BoM (they stay on the product form).
- Consumption: the BoM "Flexible Consumption" option (Allowed / Allowed with warning / Blocked) is removed; "allowed with warning" is now always the behaviour.
- Allocation report: the "Allocation Report for Manufacturing Orders" setting moves from General Settings to an option on each operation type.

## Technical data model changes

- UoM fields renamed: `product_uom_id` → `uom_id` on `mrp.bom`, `mrp.bom.line`, `mrp.bom.byproduct` and `mrp.production`; `product_uom` → `uom_id` on MRP `stock.move`. Helper `_get_default_product_uom_id` → `_default_uom_id`.
- `stock.scrap` removed: `mrp.production.scrap_ids`, `mrp.workorder.scrap_ids` / `scrap_count` and the MRP inherit of `stock.scrap` disappear. `button_scrap()` → `action_scrap()`; `action_see_move_scrap()` now targets `stock.move` with `is_scrap = True`.
- `stock.move.manual_consumption` removed; `_set_qty_producing(pick_manual_consumption_moves=True)` → `_set_qty_producing(mark_moves_picked=True)`; context key `force_manual_consumption` → `force_move_picked`.
- `mrp.workorder`: `json_popover` / `show_json_popover` replaced by `has_conflicts`, `has_planning_issues` (searchable) and `decoration_dates`; `production_reference_ids` added.
- `consumption` removed from `mrp.bom`, `mrp.production` and `mrp.workorder`.
- `mrp.unbuild.lot_id` → `lot_ids` (many2many); `product_qty` follows the selected serials; `action_clear_lots()` added.
- `mrp.bom.line`: `attachments_count`, `action_see_attachments()`, `bom_product_id`, `bom_product_qty` and `bom_product_uom_id` removed; `write()` now re-checks BoM cycles and flags outdated BoMs on running productions.
- Removed: `product.document.attached_on_mrp` (and its `ir.attachment` override), `mrp.workorder.finished_lot_ids`, `stock.move.order_finished_lot_ids`, `res.config.settings.group_mrp_reception_report`.
- Added: `stock.move.action_open_allocation_report()`; allocation reporting is driven by operation types (`auto_show_reception_report`).

## How your habits should change

- Imports, exports, integrations and reports using `product_uom_id` / `product_uom` on BoMs, MOs or their moves must use `uom_id`.
- Scrap orders become stock moves: use the Scrap menu; kits can no longer be scrapped.
- Consumption is no longer auto-updated once a move is picked: changing the produced quantity only follows unpicked moves. The "Blocked"/"Allowed" BoM modes are gone (warning only); `skip_consumption` in context still bypasses the check.
- Enable reception/allocation report display and auto-printing per operation type, not through a user group.
- Rebuild work order filters/dashboards on the new boolean flags.

## What you gain by migrating

- Planning issues and conflicts are visible and filterable at a glance.
- Scraps, moves and valuation share one model, keeping stock data consistent.
- Replace a component across many BoMs in one action.
- Unbuild several serials in a single order.
- Uniform UoM naming across logistic modules: simpler imports and customizations.
