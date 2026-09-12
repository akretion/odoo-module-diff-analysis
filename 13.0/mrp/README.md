# mrp migration guide (12.0 -> 13.0)

This guide summarises what changes for the Manufacturing (mrp) app between Odoo 12.0 and 13.0 Community. The official 13.0 release notes contain no section dedicated to this addon, so everything below is based on the source-level changes of the addon itself.

## What's new for users

- **Several finished lots per production.** You can split a manufacturing order into multiple lots/serial numbers across its work orders. A lot entered on one work order constrains the others, and the lot is pre-filled with the matching quantity on the following work orders.
- **By-products become real production lines.** You choose at which work order a by-product is created, and you can enter the quantity actually produced directly in the work order or in the produce wizard, instead of unlocking and editing stock move lines afterwards.
- **Strict or flexible component consumption** is now a setting on the Bill of Materials: either the BoM quantities must be respected exactly, or they are only indicative.
- **Clearer manufacturing order statuses**: Confirmed, Planned, In Progress, To Close, Done, Cancelled (each with a tooltip), plus a Material Availability indicator (Ready / Waiting / Waiting Another Operation). The MO status follows the work orders and produced quantities automatically.
- **Simpler planning dates** on manufacturing orders: Planned Date, Planned End Date, Deadline and Plan From, instead of the extra "scheduled" start/end dates computed from work orders.
- **Consistent data entry**: the produce wizard and the work orders now share the same line mechanism, so components and by-products behave identically in both screens.

## Technical data model changes

New abstract models `mrp.abstract.workorder` and `mrp.abstract.workorder.line` hold the code shared by the produce wizard and the work orders.

- `mrp.workorder`: new `raw_workorder_line_ids` (Components) and `finished_workorder_line_ids` (By-products), new `move_finished_ids`; `move_raw_ids` is domain-restricted to raw moves. Removed: `active_move_line_ids`, `final_lot_domain`. `final_lot_id` renamed `finished_lot_id` (oldname kept). `production_id`, `product_id`, `product_uom_id`, `product_tracking` and `qty_producing` now come from the abstract model.
- `mrp.workorder.line`: `workorder_id` + `is_finished` replaced by `raw_workorder_id` / `finished_workorder_id`.
- `mrp.production`: `state` is now a stored computed field and gains the `to_close` value; `availability` becomes `reservation_state` (the `none` value disappears); new `date_deadline`; removed `check_to_done`, `has_moves`, `consumed_less_than_planned`, `date_planned_start_wo` and `date_planned_finished_wo`.
- `mrp.bom`: `sub_products` becomes `byproduct_ids` (model `mrp.subproduct` renamed `mrp.bom.byproduct`); new `consumption` field; BoM lines now use `bom_product_template_attribute_value_ids` (`product.template.attribute.value`) instead of `attribute_value_ids`, and `valid_product_attribute_value_ids` is gone.
- `stock.move`: `subproduct_id` renamed `byproduct_id`; `unit_factor` now defaults to 1. `stock.production.lot`: `use_next_on_work_order_id` removed.
- Methods: `_generate_lot_ids` → `generate_wo_lines`, `_update_raw_moves` → `_update_moves`, `_compute_availability` → `_compute_state`, `_create_byproduct_move` removed, `_get_ready_to_produce_state` added.

## How your habits should change

- On the BoM, pick Strict (default) or Flexible consumption. Strict enforces the exact BoM quantities when validating; Flexible lets you consume more or less, with "Validate" and "Continue Consumption" buttons on work orders.
- Use the Components / By-products areas of the work order form to enter consumed quantities, lots and by-product output.
- For tracked products, register the finished lot and quantity per work order; the next work order suggests them.
- Stop changing the MO status by hand: only reservation actions remain manual. "To Close" means everything is produced but the MO is not closed yet.
- Use Planned Date / Planned End Date / Deadline / Plan From. Work order dates drive MO dates, and a planned MO (or one done/cancelled) cannot be rescheduled by moving dates.
- Variant filtering in BoMs targets product template attribute values; a BoM cannot both target a specific variant and carry attribute-filtered lines.
- Reports, filters or customisations referring to `final_lot_id`, `subproduct_id`, `availability` or `workorder_line_ids` must be updated; field renames are handled by the migration.

## What you gain by migrating

