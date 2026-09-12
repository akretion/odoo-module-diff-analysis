# mrp migration guide (12.0 -> 13.0)

This guide summarizes what changes for Manufacturing users moving from Odoo 12.0 to 13.0 (Community). No official release-note extract matched this addon, so everything below is based on the code changes delivered in 13.0.

## What's new for users
- **Cleaner production status.** A manufacturing order now shows a computed status that follows its stock moves and work orders, including a new **To Close** step between "In Progress" and "Done". Status is no longer a field you set manually.
- **Simplified material availability.** The old Available / Partially Available / Waiting / None indicator becomes **Ready / Waiting / Waiting Another Operation**. Material readiness is now checked against the components needed by the first operation.
- **Unified production recording.** The Produce wizard and work-order screens now share the same logic and the same "production lines", making component consumption and lot entry more consistent.
- **Strict or flexible BoM consumption.** Each BoM gets a **Consumption** setting (Strict by default, or Flexible). Strict requires consuming exactly the BoM quantities; Flexible lets operators consume more or less, using "Continue Consumption" on work orders.
- **Multiple finished-product lots.** A production can be split into several lots/serials across work orders. The next work order suggests the lot and quantity already produced.
- **By-products as lines.** By-products are now handled like components: they can be linked to a work order and their quantities edited directly in the Produce wizard or work order.

## Technical data model changes
- `mrp.production`: `state` is now computed (`_compute_state`) and includes `to_close`; `availability` is replaced by `reservation_state` (`oldname='availability'`, values `confirmed`/`assigned`/`waiting`). Removed: `check_to_done`, `has_moves`, `consumed_less_than_planned`.
- New abstract models: `mrp.abstract.workorder` and `mrp.abstract.workorder.line`. New model: `mrp.workorder.line`.
- `mrp.workorder`: `production_availability` now relates to `production_id.reservation_state`. New `raw_workorder_line_ids` ("Components") and `finished_workorder_line_ids` ("By-products") replace `workorder_line_ids` and `is_finished`. New `move_finished_ids`. `final_lot_id` is renamed `finished_lot_id` (`oldname`). `_generate_lot_ids` becomes `generate_wo_lines`; `_update_raw_moves` becomes `_update_moves`.
- `mrp.bom`: new `consumption` selection (`strict` default / `flexible`).
- `stock.production.lot`: removed `use_next_on_work_order_id`.
- `stock.move`: removed `_generate_consumed_move_line`; `unit_factor` now defaults to 1. Removed `_create_byproduct_move` on productions.

## How your habits should change
- **Closing productions:** instead of relying on "Mark as Done", check the new **To Close** status and close the order there.
- **Reading availability:** learn the new Ready / Waiting labels. There is no longer a "Partially Available" state; partial reservations are reflected as Waiting, with readiness evaluated on the first operation's components.
- **Consuming components:** if a BoM is Strict (the default), you must record exactly the planned quantities before validating. Switch the BoM to Flexible if you want to over- or under-consume.
- **Managing lots:** you now choose a finished lot per production step, and you can split the order into several lots across work orders. The old "Next Work Order to Use" field on lots no longer exists.
- **By-products:** enter and adjust them per work order instead of only after the finished move is posted.

## What you gain by migrating
- A production status that reflects reality, with fewer manual clicks and a clear "To Close" step.
- Better material readiness information, now based on the operation that actually starts production.
- Safer consumption: Strict BoMs guarantee the planned quantities are used, while Flexible BoMs keep shop-floor freedom.
- Full multi-lot/multi-serial production tracking across work orders.
- By-product quantities controlled at the right work order, without unlocking posted moves.
