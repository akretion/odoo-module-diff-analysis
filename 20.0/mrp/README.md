# mrp migration guide (19.0 -> 20.0)

No 19→20 release-note extract covered the `mrp` addon, so this guide is built from the module's actual code changes. Everything below applies to the Community edition.

## What's new for users

- **BoM consumption control is simplified.** The "Flexible Consumption" selector on the Bill of Materials (Allowed / Allowed with warning / Blocked) is gone. The old "Allowed with warning" behaviour becomes the only behaviour: closing a manufacturing order always reports consumption differences, and no BoM-level option can block it.
- **Clearer work order planning.** The planning popover is replaced by searchable flags for work orders planned at the same time on a work centre, or planned out of sequence or late. You can filter on them, search work orders by the MO's References, and units now appear on kanban cards.
- **Unbuild several serial numbers in one order.** An unbuild order can now take multiple serials of the same product (lot-tracked products still accept a single lot). The simplified unbuild popup is removed in favour of the standard unbuild form, with a button to clear the selected lots/serials.
- **Allocation report is configured per operation type** instead of through one Manufacturing setting. For outgoing operation types it is always off.
- **Scrapping keeps working the same way for users**, and you can now scrap a lot/serial number directly from its form.
- **Product documents are no longer managed from the BoM** — keep them on the product form.
- **Replacing a component is faster**: from a product you can open the list of every BoM line using it and edit them all in one place.

## Technical data model changes

**Uniform UoM naming.** `mrp.bom`, `mrp.bom.line`, `mrp.bom.byproduct`: `product_uom_id` → `uom_id` (plus `_get_default_product_uom_id` → `_default_uom_id`, `_compute_product_uom_id` → `_compute_uom_id`); `mrp.production.product_uom_id` → `uom_id`; on `stock.move`, `product_uom` → `uom_id` and the MRP `product_uom_id` values → `uom_id`. Any import, export, server action or report referencing the old names must be updated.

**Removed or replaced**
- The `stock.scrap` model and `mrp/models/stock_scrap.py`. Scraps are now `stock.move` records flagged `is_scrap`, with new fields `scrap_reason_tag_ids` and `should_replenish_scrapped`. `mrp.production.scrap_ids` and `mrp.workorder.scrap_ids`/`scrap_count` are gone; `button_scrap` becomes `action_scrap` (opens a `stock.move` form) and `action_see_move_scrap` returns a move list. Kits can no longer be scrapped.
- `stock.move.manual_consumption` and its helpers; `_set_qty_producing(pick_manual_consumption_moves=True)` → `_set_qty_producing(mark_moves_picked=True)`; context key `force_manual_consumption` → `force_move_picked`.
- The `consumption` field on `mrp.bom`, `mrp.production` and `mrp.workorder`.
- `mrp.workorder.json_popover` / `show_json_popover`, replaced by `has_conflicts`, `has_planning_issues`, `decoration_dates` and `production_reference_ids`.
- `mrp.unbuild.lot_id` → `lot_ids` (Many2many); `product_qty` is now computed from the number of selected serials.
- `mrp.workorder.finished_lot_ids` and `stock.move.order_finished_lot_ids`.
- `mrp.bom.line.attachments_count`, `action_see_attachments`, `mrp/models/product_document.py`, `mrp/models/ir_attachment.py` and `product.document.attached_on_mrp`.
- `mrp.bom.line.bom_product_id`, `bom_product_qty`, `bom_product_uom_id`, and the `stock.traceability.report` inherit in mrp.
- The `group_mrp_reception_report` setting in `res.config.settings`.

## How your habits should change

- Stop choosing a consumption policy on the BoM; expect the warning summary at every MO closing.
- A move you mark as picked is no longer automatically re-updated afterwards, so register consumed quantities deliberately (shopfloor, work order completion, or editing the MO/barcode).
- Find planning problems through filters and highlights rather than the old popover.
- Use the standard unbuild form and select all serials up front.
- Configure the allocation report on the operation type, not in Settings.
- Update saved filters, imports/exports and custom reports still using `product_uom_id`.
- Scrap kits through their components, not as a single kit.

## What you gain by migrating

One consistent UoM field name across manufacturing, fewer obsolete fields and settings to maintain, more predictable consumption warnings, faster component replacement across several BoMs, batch unbuilding of serials, and planning issues that are visible and searchable instead of hidden in a popover.
