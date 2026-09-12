# stock migration guide (16.0 -> 17.0)

Odoo 17.0 reworks the Inventory (`stock`) transfer flow in depth and changes several fields that other modules and customizations rely on. Here is what a 16.0 user and integrator should know.

## What's new for users

- **Revamped pickings.** The distinction between planned and immediate transfers is gone: a new transfer starts in "Ready" and can be planned by setting it back to "Draft".
- **Detailed operations, without the wizard.** Move lines are edited directly on the transfer, and you can pick existing stock (quants) from the line. Data stays on the move until the transfer is saved.
- **Flexible reservation.** You can edit reserved quantities and reserve specific quants instead of accepting what the system proposed.
- **Forecast report.** Reserving/unreserving now affects only the selected product instead of the whole operation, and the buttons support multi-step routes.
- **New removal strategy: Least Packages.** Avoids splitting a demand over two packs when one larger pack covers it.
- **MTO/MTS continuity.** When a Make-to-Order chain is interrupted, items can be reserved from available stock so the workflow continues.
- **Lots and serial numbers.** Lot/serial properties are displayed on quants, and mass entry on receipts accepts expiration dates and quantities.
- **Operations menu.** Revamped to find the right operation faster.
- **Print at operation type.** Choose which report is printed automatically at transfer validation (downloaded if no IoT printer is linked).
- **Product quantity update.** Update a product's quantity quickly from its form.
- **Replenishments.** Select a list of products and replenish them to their maximum quantity.
- **Packaging** is now displayed on transfer documents.

## Technical data model changes

- **stock.move**: `scrap_ids` (One2many) is removed and replaced by `scrap_id` (Many2one, "Scrap operation"). New computed fields `show_quant`, `show_lots_m2o`, `show_lots_text` (previously passed through the action context); `display_clear_serial` is removed. `picking_type_id` becomes editable (`readonly=False`).
- **stock.scrap**: `move_id` (Many2one) is removed and replaced by `move_ids` (One2many): one scrap order can now generate several stock moves (used for kit scrapping). `scrap_qty` loses its `precompute` and gains `default=0.0`.
- New methods `_should_check_available_qty()` and `check_available_qty()`: availability is checked through `qty_available` in the scrap's location/lot/package/owner context instead of summing gathered quants.
- **stock.picking**: `move_ids_without_package` was a computed Many2many with an inverse; it is now a plain One2many with a domain on `package_level_id`/`picking_type_entire_packs`. The helpers `_compute_move_without_package`, `_set_move_without_package` and `_get_move_ids_without_package` are deleted.
- **stock.move.line**: writing `move_id` now also updates `picking_id`; `package_level_id` is propagated when moves are created from lines.
- **stock.quant**: `display_name` no longer hides lot/package/owner depending on the user's groups.
- Views: immediate transfers use a dedicated `view_stock_move_operations_immediate`; display flags are computed on the move instead of being injected in the action context.
- Note: a large quantity/reservation refactor ("quantity pocalypse", PR 137864) also landed in this addon; its details are outside this summary.

## How your habits should change

- Stop looking for the "Immediate Transfer" flag: work from the "Ready" state and switch a transfer to "Draft" to plan it.
- Enter the same quantity in the demand and done fields; on immediate transfers, a done quantity larger than the reserved one now raises an error, and a demand of 0 means "use the done quantity".
- Edit operations in the transfer itself and click the detailed-operations button only when you need move lines; remember to save the transfer.
- Do not rely on `move.scrap_ids` / `scrap.move_id` in exports, filters, automations or custom code: use `move.scrap_id` and `scrap.move_ids` instead.

## What you gain by migrating

A simpler, faster picking flow with fewer clicks and RPC calls, precise and flexible reservation (including choosing the exact quants), smarter removal strategies and better MTO continuity. Lot/serial handling and replenishment are more efficient, and printing is automated at operation type level. Migrating now also keeps you on a supported version, with a cleaner data model for scraps and packaged moves.
