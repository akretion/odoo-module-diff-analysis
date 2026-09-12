# mrp_subcontracting migration guide (18.0 -> 19.0)

## What's new for users
The 19.0 release notes only contain one line that directly concerns this addon:

> **Subcontracting reception**: all standard inventory reception features are also available for subcontracting.

In practice, subcontracting receipts now behave like any other incoming transfer. The dedicated "Record components" button is gone, and you validate the receipt through the usual detailed operations popup. Under the hood this is a full rework of the subcontracting UX:

- A subcontract receipt for an **untracked** product has **one** subcontracting MO.
- A subcontract receipt for a **tracked** product has **one MO per lot/serial** on its move lines.
- Clicking **Register Components** opens the linked MO form (single MO) or a list of MOs (several MOs).
- MO **split** logic is reused to keep the receipt's move lines and the linked MOs in sync.

Other Manufacturing release notes items (BoM default batch size, Gantt view on MOs, operation costing, work center capacity, multiple lot numbers per MO) belong to the broader `mrp` app or to Enterprise and are **not** part of this addon.

## Technical data model changes
**Removed field**
- `stock.picking.display_action_record_components` (Selection `hide` / `facultative` / `mandatory`) — removed, along with its compute method.

**Removed location flag**
- `stock.location.is_subcontracting_location` is gone. Routing rules are no longer activated/archived per location: `_activate_subcontracting_location_rules`, `_archive_subcontracting_location_rules` and `stock.warehouse._update_subcontracting_locations_rules` were removed. A new helper `stock.location.is_subcontract()` replaces the flag.

**Changed field behaviour**
- `stock.move.is_quantity_done_editable` is now always `False` for subcontracted moves (rewritten `_compute_is_quantity_done_editable`).
- `stock.move._compute_show_info` forces `show_lots_m2o = True` and disables `show_lots_text`; `stock.picking._compute_show_lots_text` does the same at picking level. Lot/serial fields on subcontract receipts are therefore mandatory many2one fields.

**New methods (key ones)**
- `stock.move._sync_subcontracting_productions()` — enforces the link between receipt move lines and subcontracted MOs (update quantity, split, create, delete orphan MOs).
- `stock.move.line.create` / `unlink` now trigger that sync.
- `mrp.production.action_split_subcontracting()`.
- `mrp.production.write` reacts to the `mrp_subcontracting` context to push quantity/lot changes to the receipt.
- `stock.picking._get_subcontract_mo_confirmation_ctx()`, `stock.rule._get_stock_move_values(...)`.

**Removed methods** include `subcontracting_record_component`, `_update_finished_move`, `_subcontracting_filter_to_done`, `_has_tracked_component`, `_subcontract_sanity_check`, `stock.picking.action_record_components`, and on `stock.move`: `_action_record_components`, `_auto_record_components`, `_set_quantity_done`, `_set_quantity`, `_subcontracting_possible_record`, `_subcontrating_can_be_record`, `_subcontrating_should_be_record`, `_update_subcontract_order_qty`, `_reduce_subcontract_order_qty`, `_compute_display_assign_serial`, `_compute_show_details_visible`.

**Signature changes**
- `stock.move._action_confirm(merge=True, merge_into=False, create_proc=True)`
- `stock.move.action_show_subcontract_details(lot_id=None)` — now opens `mrp.production` records instead of `stock.move`.
- `_generate_serial_numbers(next_serial, next_serial_count=False, location_id=False)`
- `write(self, vals)` naming standardised.

## How your habits should change
- Stop looking for the **Record components** button on the receipt: use the standard detailed operations popup.
- For tracked products, enter lot/serial numbers as **linked records** (no free text). Each lot/serial generates its own MO.
- Changing quantities on the receipt automatically updates, splits, creates or deletes the linked MOs — no manual MO housekeeping.
- Adding/removing a move line resynchronises the MOs immediately.
- The "done quantity" on a subcontract move is no longer editable directly; work through the move lines.
- **Register Components** now shows MO(s), not the raw material move list.

## What you gain by migrating
- Subcontracting finally uses the **standard reception UX**, so training and day-to-day work match your other receipts.
- Receipt and MO data are **always consistent**, maintained automatically instead of by manual recording steps.
- **One MO per lot/serial** gives clean, per-unit traceability on tracked subcontracted products.
- Fewer user errors and less support: quantities, backorders and component recording are handled by the system.
