# mrp migration guide (13.0 -> 14.0)

Scope: Manufacturing (Community). The official 14.0 release notes contain no entry dedicated to this addon: the Manufacturing highlights published for 14.0 concern features that do not live in Community `mrp` (work order tablet view, quality/expiry flows), so they are not presented here as included. Everything below comes from the actual 13.0 -> 14.0 code changes.

## What's new for users

- **Operations live on the BoM.** The standalone "Routing" object (`mrp.routing`) is removed. Operations are recorded directly on the Bill of Materials, on the same form as components and by-products.
- **One screen to produce.** The "Produce" wizard is gone. You set the "Quantity Producing" and the finished Lot/Serial Number on the manufacturing order itself, then post it; the flow now looks like a transfer.
- **Real backorders.** Finishing an MO before producing the full quantity opens a backorder wizard, like deliveries. The remainder becomes a new MO named `MO/xxxx-001`, linked to the original through the same procurement group, and can be planned and executed normally.
- **Flexible consumption by default.** New BoMs default to *Flexible* consumption instead of *Strict*: consuming more or less than the BoM quantity no longer blocks you by default.
- **Clearer dates.** One Scheduled Start/End date and one Deadline per MO, propagated from the linked sale/purchase document.

## Technical data model changes

Removed models: `mrp.routing`, `mrp.workorder.line`, `mrp.abstract.workorder`, `mrp.abstract.workorder.line`, produce wizard.

- `mrp.bom`: `routing_id` replaced by `operation_ids` (One2many, copied with the BoM). `mrp.routing.workcenter` is now parented by `bom_id` instead of `routing_id`.
- `mrp.bom.line` / `mrp.byproduct`: `routing_id` removed; `operation_id` filtered by a new computed `allowed_operation_ids`.
- `mrp.production`: added `qty_producing`, `lot_producing_id`, `product_tracking`, `move_byproduct_ids`, `backorder_sequence`, `mrp_production_backorder_count`, `is_partially_planned`, `action_generate_serial()`; removed `routing_id`, `workorder_count`, `bom_has_operations`, `delay_alert`, `propagate_date`, `propagate_date_minimum_delta`, `open_produce_product()`. `date_planned_start` / `date_planned_finished` are no longer computed from moves; `date_deadline` became stored, computed/inverse and read-only.
- `mrp.workorder`: no longer inherits `mrp.abstract.workorder`; `qty_producing` and `finished_lot_id` are now computed/inverse proxies on the MO; `raw_workorder_line_ids`, `finished_workorder_line_ids`, `allowed_lots_domain` removed; `consumption` field moved here.
- `stock.move` / `stock.move.line`: `lot_produced_ids` (Many2many) removed in favour of `produce_line_ids`; `done_move`, `needs_lots`, `finished_lots_exist`, `routing_id` removed; `reference` is now computed.
- `procurement.group`: `mrp_production_id` renamed `mrp_production_ids`.
- Signatures: `post_inventory()` -> `_post_inventory(cancel_backorder=False)`; `_generate_backorder_productions(close_mo=True)`; `_get_duration_expected(workcenter_id)`; `_strict_consumption_check` now lives on `mrp.production`.
- Stock moves: `date_expected` merged into `date`.

## How your habits should change

- Stop using the Produce wizard button; produce from the MO form.
- Maintain operations on the BoM. Operations can no longer be shared between a kit BoM and its parent, and they are no longer merged when planning kit components.
- On Community, component lots are no longer entered per work order (work order lines were removed): lots and quantities are handled at MO level. Per-operation lot entry remains an Enterprise tablet feature, not part of this edition.
- Scheduled Date becomes read-only once an operation is planned: reschedule the work orders instead of the MO.
- The auto-rescheduling flags ("Propagate Rescheduling", "Reschedule if Higher Than", "Delay alert") are gone; rely on Deadline propagation.
- Backordered MOs get a `-001` suffix, so references differ from 13.0.
- Plan a data migration: routings must be converted to BoM operations, and any customisation on `mrp.workorder.line` or `lot_produced_ids` must be reworked.

## What you gain by migrating

- Faster data entry: quantity, lot and posting on a single MO screen.
- Proper backorder handling with linked MOs and full traceability.
- Simpler master data: one BoM holds components, by-products and operations.
- Predictable scheduling: deadline-driven dates, no surprise auto-rescheduling.
- Less technical debt and closer alignment with stock flows, which eases future upgrades.
