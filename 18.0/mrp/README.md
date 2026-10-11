# mrp migration guide (17.0 -> 18.0)

## What's new for users

The 18.0 release-notes extract we were given covers only eCommerce (Click & Collect, mega menus, product ribbons, wishlists…). Nothing in it concerns Manufacturing, so it is not reproduced here: no eCommerce feature should be assumed to ship with the Community `mrp` addon. The user-visible changes below therefore come from the addon diff itself.

- **Operation dependencies.** A new "Operation Dependencies" flag on the BoM chains operations together; those dependencies are pushed onto work orders at confirmation. If left unticked, Odoo assumes all operations can start simultaneously.
- **Work centers.** New per-product capacity table and work center tags.
- **Lead times on the BoM.** "Manufacturing Lead Time" (in days) and "Days to prepare Manufacturing Order", with an action to compute the latter automatically.
- **By-products.** Cost shares must be positive and total no more than 100 %.
- **Kits vs reordering rules.** A kit-type BoM is now refused if the product has a reordering rule.
- **Manual consumption becomes a choice.** On tracked components it is no longer forced on and read-only: the BoM line flag is a plain editable boolean. The picking type option "Consume Reserved Lots/Serial Numbers automatically" is removed.
- **Documents.** `mrp.document` disappears: product/BoM documents are unified in `product.document`, with a new "MRP: Visible at" setting (Hidden / Bill of Materials), shown in the BoM and production chatter.

## Technical data model changes

- Renamed model: `stock.production.lot` → `stock.lot`; new `stock.quant` extension.
- Removed model `mrp.document`, replaced by `product.document` (with `attached_on_mrp`) plus an `ir.attachment` extension.
- Removed fields: `mrp.bom.line.manual_consumption_readonly`, `stock.picking.type.use_auto_consume_components_lots`, `mrp.production.use_auto_consume_components_lots`, `mrp.workorder.next_work_order_id`.
- `mrp.bom` now inherits `product.catalog.mixin`; `_order` is `sequence, id`; name search covers the reference and the product; `product_qty` uses the *Product Unit of Measure* rounding; `ready_to_produce` default changed from `asap` to `all_available`.
- New `mrp.bom` fields: `possible_product_template_attribute_value_ids`, `allow_operation_dependencies`, `produce_delay` (Integer), `days_to_prepare_mo`.
- New dependency fields: `blocked_by_operation_ids` / `needed_by_operation_ids` (routing) and `blocked_by_workorder_ids` / `needed_by_workorder_ids` (relation `mrp_workorder_dependencies_rel`).
- New models `mrp.workcenter.capacity` and `mrp.workcenter.tag`, exposed on the work center via `capacity_ids` / `tag_ids`.
- `mrp.production`: `unbuild_ids` added; the finished-lot field is replaced by `production_ids`.
- Method signatures: `mrp.bom._bom_find(products, …)` now takes a recordset and returns a per-product mapping of BoMs; `_bom_find_domain(products, …)` follows; `mrp.production._set_qty_producing(pick_manual_consumption_moves=True)`.
- New constraints: BoM cycle detection, "Apply on Variant" consistency across lines, operations and by-products, and kit vs orderpoint.

## How your habits should change

The release notes contain no MRP entry, so the diff is your reference:

- Decide per component whether tracked products really need manual consumption; Odoo no longer decides for you, and there is no picking-type switch left to auto-consume reserved lots.
- Review BoMs whose readiness relied on the old default (components for the first operation available).
- Attach documents on the product and set "MRP: Visible at = Bill of Materials", instead of maintaining the former MRP document list.
- Clean up kit BoMs before upgrading: products with reordering rules are now rejected.
- Adapt any customisation calling `_bom_find` / `_bom_find_domain`.

## What you gain by migrating

- Dependency-aware planning: operations and work orders follow a realistic sequence.
- Better capacity planning with per-product work center capacities and tags.
- A single document system for products, BoMs and productions, visible in the chatter.
- Explicit, predictable consumption rules for tracked components.
- Safer, faster data: BoM cycle detection, by-product cost-share checks and optimised multi-level BoM explosion.
