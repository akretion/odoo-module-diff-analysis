# mrp_subcontracting migration guide (18.0 -> 19.0)

## What's new for users

Odoo 19 reworks subcontracting receivables. The official 19.0 release notes state it plainly: **"All standard inventory reception features are also available for subcontracting."** Concretely, in the Community edition:

- Subcontracted receipts now behave like any other receipt: opening a move shows the standard detailed operations popup, with the lot/serial and quantity handling you already know from regular receipts.
- Lots and serials are picked through a real *Lot/Serial Number* field (many2one) on receipt move lines instead of free-text entry. Fewer typos, guaranteed valid numbers.
- One subcontracting MO per receipt: untracked products get a single subcontracting MO; tracked products get **one MO per lot/serial**.
- "Register Components" opens a simplified manufacturing order form when a single MO is linked, or a list of MOs when several are.

Also relevant from the Manufacturing notes: several lot/serial numbers can be generated from one MO, and MO splitting has been made more user friendly — subcontracting reuses both mechanisms to keep receipts and MOs in sync.

## Technical data model changes

**Field removed:** `display_action_record_components` (Selection) on `mrp.production`.

**Method signature / behaviour changes:**
- `stock.move.action_show_subcontract_details()` becomes `action_show_subcontract_details(lot_id=None)`: it now opens `mrp.production` records (a simplified MO form, or a list when several MOs are linked) instead of raw `stock.move` lines.
- New `stock.move._sync_subcontracting_productions()`: enforces synchronisation between receipt move lines and their subcontracting MOs (quantity per lot, MO creation/update/deletion using the MO split mechanism, always keeping at least one MO available for splitting).
- New `mrp.production.action_split_subcontracting()`.
- Removed overrides and helpers: `subcontracting_record_component`, `_update_finished_move`, `_subcontracting_filter_to_done`, `_has_tracked_component`, `_subcontract_sanity_check`, `_auto_record_components`, `_action_record_components`, `_subcontrating_should_be_record`, `_subcontrating_can_be_record`, `_subcontracting_possible_record`, `_has_tracked_subcontract_components`, `_update_subcontract_order_qty`, `_reduce_subcontract_order_qty`, `_compute_display_assign_serial`.
- `is_quantity_done_editable` is now always `False` on subcontracting moves; quantities are edited in detailed operations. `show_subcontracting_details_visible` becomes `True` as soon as the move has move lines and linked MOs.
- Lot handling is forced to many2one on subcontracting moves (`force_lot_m2o`, `show_lots_text = False`, `show_lots_m2o = True`), including when generating serial numbers.

## How your habits should change

- Stop typing lot/serial names in a text field on the receipt: choose or create a lot/serial in the *Lot/Serial Number* field.
- Stop recording components from the receipt move quantity field — it is no longer editable there. Use the detailed operations popup, then "Register Components".
- With tracked products, expect **one MO per lot/serial** instead of a single MO covering several lots: recording components becomes a list of MOs rather than one form.
- Changing receipt quantities or splitting a receipt now automatically creates, resizes or removes the matching subcontracting MOs; you rarely need to open an MO to fix quantities.
- Portal (subcontractor) users get the same simplified flow.

## What you gain by migrating

- **One consistent process:** subcontracted receipts follow the same reception flow as any other receipt — less training, fewer special cases for warehouse staff.
- **Reliable traceability:** real lot/serial records linked to move lines, with one MO per lot/serial, so production and reception are always aligned.
- **Fewer manual corrections:** quantities, splits and linked MOs stay synchronised automatically instead of being patched by hand.
- **A maintained code base:** the old, fragile "record components" workarounds are gone, replaced by the standard reception and MO-splitting machinery supported in 19.0.
