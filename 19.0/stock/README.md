# stock migration guide (18.0 -> 19.0)

This guide summarizes what changes in the Inventory (`stock`) addon between Odoo 18.0 and 19.0. It targets functional users and customers planning an upgrade (Community edition).

## What's new for users

- **Packages inside packages**: boxes can be put on a pallet, a package containing other packages is moved in one go, and a package history records what was moved in previous transfers.
- **Packagings merged with units of measure**: `product.packaging` disappears. Packagings are now units of measure added on the product and selectable on orders and stock moves. UoM categories no longer exist, so buying in pieces and selling in kilograms is no longer blocked.
- **Simplified locations**: "View" becomes "Virtual"; the "Is a Scrap Location" flag is replaced by the Inventory Loss location type; the Comment field and X/Y/Z positions are gone.
- **Reordering rules**: a company-level replenishment horizon (365 days by default) replaces the hidden visibility days; rules show a deadline date and data previews, and default their multiple to the vendor's or BoM's unit. Ordered quantities may exceed the maximum when rounding to a multiple.
- **Lots and serial numbers**: easier navigation from a partner to their lots/serials, vendor and customer names directly in the traceability report, reworked Lot/Serial form view.
- **Quantity on hand** can be updated from the product form; **reservation** is triggered right after an inventory adjustment is validated.
- Transfers gain a **Late Availability** filter; picking operation and delivery slip reports were improved. The MTO route is activated through a dedicated setting.

## Technical data model changes

- `stock.quant.package` is renamed `stock.package`; a new `stock.package.history` model stores past package hierarchy.
- `stock.package_level` is removed: packages are handled on move lines. Removed: `stock.move.package_level_id`, `stock.move.line.package_level_id`, `stock.picking.package_level_ids(_details)`, `move_ids_without_package`, `move_line_ids_without_package`.
- `stock.package` gains hierarchy and destination fields: `parent_package_id`, `child_package_ids`, `all_children_package_ids`, `package_dest_id`, `child_package_dest_ids`, `outermost_package_id`, plus `complete_name`/`dest_complete_name`, `contained_quant_ids`, `content_description`, `location_dest_id`, `move_line_ids`, `picking_ids`.
- `stock.move`: adds `package_ids`, `has_lines_without_result_package`, `packaging_uom_id`, `packaging_uom_qty`; removes `picking_type_entire_packs`, `scrapped`, `product_packaging_id`, `product_packaging_qty/_quantity`; `group_id` becomes `reference_ids` (new `stock.reference` model replacing `procurement.group`).
- `stock.move.line`: adds `package_history_id`, `result_package_dest_name`, `is_entire_pack`, `scrap_id`; removes `is_scrap`, `product_packaging_qty`, `picking_type_entire_packs`.
- `stock.rule` loses `group_propagation_option`, `group_id`, `propagate_warehouse_id`; reordering rules lose `group_id`.
- `product.packaging` is removed; `uom.uom` gains `package_type_id` and `route_ids`; `stock.route.packaging_selectable` becomes `package_type_selectable`; `stock.package.type.route_ids` is added.
- `stock.location`: `scrap_location`, `comment`, `posx/posy/posz` removed; usage labels renamed.
- Reordering rules: `visibility_days` removed, `lead_days_date` becomes `lead_horizon_date`, new company setting `horizon_days`.
- `stock.lot.last_delivery_partner_id` becomes `partner_ids`.
- Signatures: `_put_in_pack(package_id, package_type_id, package_name)`, `action_put_in_pack(*, ...)`, `_to_pack(without_pack=True)`, `_get_reserve_quantity()` without `product_packaging_id`, `_search_rule(..., packaging_uom_id, ...)`, `get_visibility_days()` → `get_horizon_days()`.

## How your habits should change

- Whole packages are no longer listed separately from operations: everything is picked and moved from the move lines.
- To scrap, set a location to type Inventory Loss instead of ticking "Is a Scrap Location"; review existing scrap locations.
- Define packagings as units of measure on the product; UoM categories no longer restrict which unit can be used on a move.
- Procurement groups are gone: grouping now relies on references, batches/waves and the partner setting for RFQs.
- Set the replenishment horizon on the company; per-rule visibility days no longer exist.
- Customizations touching `stock.package_level`, `procurement.group`, `product.packaging` or removed package/location fields must be rewritten.

## What you gain by migrating

- Real pallet and nested package support, with history of what was moved.
- One concept for units and packagings: fewer blocking rules, more flexibility.
