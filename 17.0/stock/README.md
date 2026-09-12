# stock migration guide (16.0 -> 17.0)

Scope: the Odoo 17.0 Community `stock` addon. This guide combines the official 17.0 release notes with the actual code changes shipped in the module.

## What's new for users

- **Revamped transfers.** The distinction between planned and immediate transfers is gone. Every transfer starts in *Ready* state; set it back to *Draft* to plan it. Detailed operations now let you select existing stock directly.
- **Flexible reservation.** Reserved quantities can be edited by hand, and you can reserve specific quants.
- **Forecast report.** Reserving or unreserving from the forecast now affects only the selected product, not the whole operation.
- **Mass entry of lots/serials.** On receipts, pasting a list of lots/serials now also accepts expiry dates and quantities.
- **Packaging on documents.** Packaging appears on purchase orders, sales orders and transfers; the incoterm and location are added to the delivery slip.
- **Revamped operations menu** to reach the right operation type faster.
- **New "Least Packages" removal strategy** to avoid splitting one demand across two packs when a larger pack is enough.
- **Kit scrapping** (with Manufacturing): a scrap order on a kit can now scrap the kit's components instead of the kit itself.

## Technical data model changes

*stock.move.line*
- `qty_done`, `reserved_qty` and `reserved_uom_qty` are merged into a single `quantity` field: reservation and done quantity are no longer separate columns.
- Decreasing a quantity to zero now unlinks the move line.
- Forecast and weight computations now use `quantity` (locations use `quantity_product_uom`).

*stock.move*
- `quantity_done` → `quantity`, computed from move lines and still editable. `_quantity_done_compute`, `_quantity_done_set` and `_quantity_done_sml` were renamed `_compute_quantity`, `_set_quantity` and `_quantity_sml`.
- New stored, editable `picked` boolean: a move is picked when a line is picked or when the move is done.
- Removed: `reserved_availability`, `move_line_nosuggest_ids`, `show_reserved_availability`, `from_immediate_transfer`, `display_clear_serial` and the `_get_move_lines()` helper.
- `product_packaging_qty_done` → `product_packaging_quantity`.
- New computed `show_quant`, `show_lots_m2o` and `show_lots_text` replace the context flags previously passed to the detailed operations window.

*Other models*
- `stock.scrap.move_id` (Many2one) becomes `move_ids` (One2many), and `stock.move.scrap_ids` becomes `scrap_id`; new `check_available_qty()` / `_should_check_available_qty()` helpers.
- `stock.picking.move_ids_without_package` is now a plain One2many with a domain instead of a computed Many2many with an inverse.
- `is_initial_demand_editable` now depends on `picking_id.is_locked`.

## How your habits should change

- **One quantity column:** what you reserve is what you pick. Tick *Picked* to mark a move as done.
- **Plan explicitly:** the *Immediate Transfer* checkbox is gone; transfers start ready and you set them to draft when you want to plan them.
- **Reservation may exceed stock:** quantities follow what you pick. Odoo never suggests it, so "reserved > on hand" is a warning, not a bug.
- **You can no longer pick a single move line** — an acknowledged regression of the merged-quantity design.
- **Immediate transfers:** if a demand quantity is set and smaller than the done quantity, validation raises an error.
- **Detailed operations** now open as a standard record form rather than a wizard.
- **Kit scrapping:** the scrap form now asks which BoM to explode.

## What you gain by migrating

- A simpler picking screen with one quantity column: less training, fewer reservation/done mismatches.
- Faster data entry: detailed operations without extra RPC round-trips, and mass lot/serial entry including expiry dates.
- Cleaner documents (packaging, incoterm, location) and better traceability.
- More accurate scrap flows for kits and a smarter "Least Packages" removal strategy.
- Fewer manual corrections: Odoo never overwrites a move you have already picked.
