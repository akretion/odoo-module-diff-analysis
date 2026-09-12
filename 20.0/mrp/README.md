# mrp migration guide (19.0 -> 20.0)

## What's new for users

No official release-note extract covers this addon for 20.0, so the points below are derived from the code changes actually shipped in the `mrp` module.

- **Scrap is no longer a separate document.** The `stock.scrap` model is removed; scrapping a component, a finished product or a by-product now creates a stock move flagged as a scrap. The Scrap menu, the *Scrap* button on a manufacturing order and on a work order all open this new list/form. Functionally the flow is unchanged (product, quantity, reason, replenishment). One regression to know about: **kits can no longer be scrapped**.
- You can now scrap a lot/serial number directly from its own form view.
- **Work-order planning** gained explicit "planning issues" and "conflicts" information, plus plan / unplan actions and a "select MO to plan" action.
- **Unbuild** orders can cover several serial numbers at once, with a "clear lots" action.
- The **consumption warning** field is dropped from BoMs and the manual-consumption detection helpers are gone; component replacement across BoMs is faster.
- **Subcontracting**: order-level "finished lot ids" fields removed. **Product documents** are no longer shown on the BoM form (they remain on products). The **allocation report** is now driven by a setting per picking type.

## Technical data model changes

- Removed: `stock.scrap` (file `mrp/models/stock_scrap.py`), `mrp.production.scrap_ids`, `mrp.workorder.scrap_ids` and `mrp.workorder.scrap_count`. `mrp.production.scrap_count` remains but is computed from scrap moves (`is_scrap = True`) on components and finished products.
- `stock.move`: new `is_scrap`, `scrap_reason_tag_ids`, `should_replenish_scrapped`; new helpers `_prepare_scrap_move_vals`, `_action_replenish`, `_get_production_assignation_domain`, `action_add_from_catalog_wo`.
- **UoM renames**: `product_uom_id` -> `uom_id` on `mrp.bom`, `mrp.bom.line`, `mrp.bom.byproduct`, `mrp.production`, `mrp.unbuild`, `mrp.workcenter.capacity`; `mrp.workorder.uom_id` is now related to `production_id.uom_id`; on MO moves `product_uom` -> `uom_id`. `_compute_product_uom_id` -> `_compute_uom_id`, `_get_default_product_uom_id` -> `_default_uom_id`.
- **Signatures**: `mrp.production.button_plan(as_soon_as_possible=True)`, `_plan_workorders()` (no `replan`), `_split_productions(...)` (no `skip_procurement`), `_set_qty_producing(mark_moves_picked=True)`, `_autoprint_generated_lots(lot_ids)`, `button_scrap` -> `action_scrap`. `mrp.workorder._set_duration(employee_id=None, employee_duration=None)`, `_calculate_date_finished(..., compute_leaves=False)`, `_get_current_theorical_operation_cost` -> `_get_current_theoretical_operation_cost`. `mrp.bom._set_outdated_bom_in_productions(skip_bom_outdated_unmark=False)`.
- **New MO fields/actions**: `packages_count`, `remaining_time`, `all_child_count`, `note`, `active_workcenter_ids`; `action_view_packages`, `action_view_allocation_report`, `action_detailed_operations`, `action_view_mrp_production_all_childs`. New BoM helpers: `action_copy_existing_operations`, `action_open_exploded_bom_lines`, `_compute_component_count`, `_compute_subassembly_count`, `_get_exploded_bom_data`.

## How your habits should change

- Stop thinking in "scrap orders": the Scrap menu now lists stock moves (existing scrap data is handled by the upgrade).
- Scrapping a kit is no longer possible; scrap the components instead.
- Any data import/export, saved filter, custom report or Studio/OCA customization using `product_uom_id` (BoM, BoM line, by-product, MO, unbuild, work-center capacity) or `product_uom` on MO moves must switch to `uom_id`.
- Custom code calling `button_scrap`, `_get_default_product_uom_id`, `_get_current_theorical_operation_cost`, `_plan_workorders(replan=...)` or `_split_productions(skip_procurement=...)` needs updating.
- Component consumption is registered through stock moves only; the older manual-consumption flags/helpers no longer exist. Customizations relying on BoM consumption warnings must be dropped.

## What you gain by migrating

- One single object (the stock move) for every stock outflow: scrap quantities, valuation, traceability and replenishment then follow your normal logistics rules.
- A consistent `uom_id` naming across logistic models, making imports, exports and integrations simpler and less error-prone.
- Better visibility on work-order planning conflicts, with explicit plan/unplan actions.
- More flexible unbuilds (several serials in one order) and faster BoM component replacement on large bills of materials.
