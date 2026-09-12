# mrp migration guide (13.0 -> 14.0)

## What's new for users

No official release-note extract covered this addon, so the overview below is derived from the code changes themselves.

- **Backorders on manufacturing orders.** An MO can now be produced partially: enter the produced quantity, click Mark Done, and Odoo offers to create a backorder MO for the remainder. Backorders share the original procurement group, receive a suffixed reference (e.g. `MO/0001-001`) and are listed through a "Backorder MO's" button. Reservations already made are reused by the backorder.
- **Producing happens directly on the MO.** The separate "Produce" wizard is gone. You now fill in "Quantity Producing" and the finished Lot/Serial Number on the MO (with a "Generate Serial" button for tracked products); byproducts get their own tab. Consumed quantities follow automatically as you change the produced quantity.
- **Consumption is more flexible by default.** New BoMs default to *Flexible* consumption instead of *Strict*.
- **Planning.** The Scheduled Date is now prefilled from the deadline, and a MO whose scheduled date is in the past is planned as soon as possible.

## Technical data model changes

**Models**
- `mrp.routing` is removed. Operations (`mrp.routing.workcenter`) are attached directly to the BoM via the new required `bom_id` field.
- `mrp.bom.routing_id` is replaced by `operation_ids` (One2many, copied with the BoM); `mrp.bom.line` and `mrp.bom.byproduct` lose `routing_id` and gain the computed helper `allowed_operation_ids`.
- `procurement.group.mrp_production_id` renamed to `mrp_production_ids`.

**Fields added** (MO): `lot_producing_id`, `qty_producing`, `product_tracking`, `move_byproduct_ids`, `backorder_sequence`, `mrp_production_backorder_count`, `is_partially_planned`; `should_consume_qty` on `stock.move`.

**Fields removed**: `routing_id` (BoM, BoM line, byproduct, stock.move, MO), `bom_has_operations`, `workorder_count`, `delay_alert`, `propagate_date`, `propagate_date_minimum_delta`, `stock.move.line.lot_produced_ids`, `done_move`, `stock.move.needs_lots`, `finished_lots_exist`.

**Behaviour changes**
- `date_deadline` on the MO becomes stored, read-only and computed from finished moves; it is propagated to moves. `date_expected` on stock moves is merged into `date`.
- BoM `consumption` default: `strict` -> `flexible`.
- `operation_id` domains on BoM lines, byproducts and stock moves now rely on `allowed_operation_ids`.

**Methods**
- `post_inventory()` -> `_post_inventory(cancel_backorder=False)`.
- `button_mark_done()` is now multi-record and may return a backorder action; new helpers `_pre_button_mark_done()`, `_get_quantity_to_backorder()`, `_generate_backorder_productions()`, `_check_sn_uniqueness()`.
- `open_produce_product()` and `_strict_consumption_check()` removed.

## How your habits should change
- Maintain operations on each BoM: routings are no longer a reusable library. If several BoMs shared one routing, duplicate the operations on each of them; parent/kit-BoM routing sharing is gone too.
- To finish an MO, set Quantity Producing and Mark Done, then confirm the proposed backorder when a quantity is left.
- Treat Deadline as the read-only promise date coming from sales/purchases; use Scheduled Date and work-order leaves to schedule. The old automatic rescheduling flags are gone.
- Re-check BoMs that relied on Strict consumption.

## What you gain by migrating
- Genuine partial-production flows: traceable backorders, reused reservations, no manual splitting.
- Simpler BoM maintenance — operations live in one object, with no separate routing records.
- Fewer clicks on the shop floor (no produce wizard) and better lot/serial handling, byproducts included.
- Cleaner dates: one scheduled date per MO/work order, deadline driven by the customer promise.
- Support: 13.0 is out of maintenance; 14.0 keeps you on a maintained branch, a prerequisite for later upgrades.
