# stock migration guide (17.0 -> 18.0)

## What's new for users

**Pull to push rules and flexible routes.** Push rules are now triggered when a transfer is *validated*, not when it is confirmed. Intermediate transfers are no longer created in advance, so your pipeline is not cluttered with transfers waiting for another one. The default multi-step receipt and delivery routes were redesigned around this principle.

**Make to stock and order.** With a rule set to "Take from stock, if not available, trigger another rule", the procurement is now launched only for the quantity missing on the new move, instead of the whole line.

**Create backorders in advance.** You can split a transfer before validating it, to ship to two different locations or on two different dates.

**Next transfer button.** A smart button on transfers shows the next linked transfers.

**Packages and locations.** Change a package's location without creating a transfer, move packages from the Kanban view, and see which locations are empty from the locations list.

**Putaway rules.** Incoming products are put away where other quantities of the same product are or have been.

**Lots and serials across companies.** Lot/serial numbers remain traceable through inter-company transfers.

**Tracking at delivery.** Products that are tracked but not tracked at reception appear as non-tracked products in transfers.

**Inventory dashboard and reports.** Both menus were reworked for readability. Users with the Inventory user right can now apply inventory adjustments.

## Technical data model changes

- `stock.move.location_dest_id` is now a **stored computed field** ("Intermediate Location"), defaulted from `picking_id.location_dest_id` or `picking_type_id.default_location_dest_id` — not from the rule anymore.
- New `stock.move.location_final_id` ("Destination Location"): the final point the chain targets. It is used for forecasting, for merges (added to the merge distinct fields) and for push decisions.
- New `stock.rule.location_dest_from_rule` (boolean): when set, the move destination comes from the rule instead of the operation type. This is the migration escape hatch that keeps 17.0 pull configurations working.
- Removed fields: `show_reserved` on `stock.picking`, `stock.picking.type` and `stock.move`, plus the putaway-rule code that enabled them.
- Product typing: the `product` value of the product `type`/`detailed_type` selection and the `stock.move.product_type` field were removed. They are replaced by `product.template.is_storable` ("Track Inventory"). `tracking = 'none'` is relabelled "By Quantity".
- Removed methods: `stock.move._create_extra_move`, `stock.move._check_unlink_move_dest`, `stock.picking._onchange_locations`. Added: `stock.move._skip_push`, `stock.move._break_mto_link`, `stock.location._child_of`.
- `_push_apply` now confirms the moves it creates and is called at **validation** (`_action_done`), not at confirmation.
- Negative moves are no longer turned into return moves at confirmation: their links are cleared and the moves are unlinked.

## How your habits should change

- Check your routes: receipts and deliveries in 2/3 steps now mix pull and push. Vendor -> Input -> Quality -> Stock and Pick -> Pack -> Ship are handled by push rules. Pull remains fully supported and is still the better choice when you plan delays, because only pull rules carry a delay.
- Since push transfers are created at validation, you will see fewer draft transfers and no more "dead" move tails, but you cannot pre-plan each intermediate transfer anymore.
- Forecasts now rely on the move's final destination. There is no check that a product is still heading to that location, so a forecast can be off until the chain completes.
- Because `location_dest_id` comes from the operation type, you need a distinct operation type per destination — unless you tick "Destination location origin from rule".
- Sales order lines propagated to the pick prevent automatic merging of moves.
- Products are no longer "Storable/Consumable": use a Goods product with the "Track Inventory" checkbox.

## What you gain by migrating

Fewer automatically created objects and no orphan transfers, a warehouse flow you can deviate from until the last moment, partial MTO/MTS procurements that match real shortages, faster backorder splitting, a clearer inventory dashboard and reporting menu, better putaway, cross-company traceability of lots and serials, and a simpler product model where "Track Inventory" says exactly what it does.
