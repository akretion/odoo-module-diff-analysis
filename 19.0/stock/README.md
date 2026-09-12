# stock migration guide (18.0 -> 19.0)

## What's new for users
- **Packages within packages**: a box can be placed on a pallet; moving the pallet moves everything it contains. Packages are now handled directly on transfer operations, so the separate "package level" concept disappears.
- **Put in pack wizard**: when the operation type requires it, you can choose an existing package, a package type and/or a name.
- **Packagings merged with units of measure**: define packagings as units of measure on the product. They are also selectable in eCommerce.
- **Locations simplified**: unnecessary default virtual locations are gone, location types are relabelled (Virtual, Vendor, Customer, Transit...), and "Replenish Location" is renamed "Replenishments".
- **Lots/Serial numbers**: better navigation from customer serial numbers, reworked Lot/Serial form, product-specific lot/serial numbers, and a partner smart button.
- **Traceability report**: shows vendor/customer names and the warehouse short code.
- **Reordering rules**: new "Replenishment Horizon" (company setting, 365 days by default), a deadline date, and suggested multiples taken from the vendor pricelist or bill of materials.
- Borrowing from the release notes that apply here: physical inventory view simplified, reservation triggered right after an adjustment is validated, product quantity on hand editable from the product form, improved Picking Operation and Delivery Slip reports, MTO route activated through a setting.

## Technical data model changes
**Models**
- `stock.quant.package` renamed to `stock.package` (moved to its own file).
- New `stock.package.history` (tracks what was moved, with past hierarchy).
- `stock.package_level` removed entirely.
- New `stock.reference` (name, move_ids, picking_ids) replaces `procurement.group`.

**Removed**
- `procurement.group`; `group_id` on `stock.move`, `stock.picking`, `stock.orderpoint`, `stock.rule`; `group_propagation_option` and `propagate_warehouse_id` on rules.
- `package_level_id` on moves/move lines; picking fields `move_ids_without_package`, `move_line_ids_without_package`, `package_level_ids`, `package_level_ids_details`; `picking_type_entire_packs`.
- `product.packaging` (merged into `uom.uom`), and `product_packaging_id` / `product_packaging_qty` / `product_packaging_quantity` on stock moves.
- `stock.location.scrap_location`, `stock.move.scrapped`, `stock.move.line.is_scrap`; `stock.lot.last_delivery_partner_id`; UoM categories and `product_uom_category_id`; location `posx/posy/posz` and `comment`.

**Added / changed**
- `stock.package`: `complete_name`, `dest_complete_name`, `content_description`, `parent_package_id`, `child_package_ids`, `all_children_package_ids`, `package_dest_id`, `outermost_package_id`, `location_dest_id`, `move_line_ids`, `picking_ids`, `json_popover`.
- `stock.move.line`: `package_history_id`, `result_package_dest_name`, `is_entire_pack`. `stock.move`: `reference_ids`, `packaging_uom_id`, `packaging_uom_qty`. `stock.picking`: `reference_ids`.
- Scrap locations are now simply locations with `usage = 'inventory'`; `stock.rule` hosts the `Procurement` type and `run()` (previously on `procurement.group`).
- Reordering rules: `visibility_days` removed, company `horizon_days` added, `lead_days_date` renamed `lead_horizon_date`.
- `stock.lot.last_delivery_partner_id` becomes `partner_ids` (many2many). `uom.uom` gains `package_type_id`/`route_ids`; moves, move lines and scraps gain `allowed_uom_ids`; `stock.route.packaging_selectable` becomes `package_type_selectable`.
- Method signatures: `_put_in_pack(package_id, package_type_id, package_name)`, `action_put_in_pack(...)`, `_pre_put_in_pack_hook(...)`, `_to_pack(without_pack=...)`; `_get_reserve_quantity()` no longer accepts `product_packaging_id`.
- Shipping connector settings now install the REST API modules (`module_delivery_fedex_rest`, `..._ups_rest`, `..._usps_rest`).

## How your habits should change
- Pack and unpack from transfer operation lines; stop managing package levels. A package can be both source and destination.
- Create packagings as units of measure on the product instead of product packagings.
- For scrap, select a location of type Inventory Loss instead of ticking "Is a scrap location".
- Use the company replenishment horizon instead of visibility days.
- Filter lots/serials by customer or vendor from the partner record.
- Review saved filters, reports and favourites using renamed location types or removed fields.

## What you gain by migrating
- Real box/pallet hierarchies, removing manual workarounds for multi-level packaging.
- A single concept for units and packagings, with more flexible units across purchase, sales and stock.
- Simpler, less error-prone location and scrap configuration.
