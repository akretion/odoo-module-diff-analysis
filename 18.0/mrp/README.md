# mrp migration guide (17.0 -> 18.0)
## What's new for users
The official 18.0 release notes extract provided covers eCommerce, not MRP. No relevant MRP-only release note is available here, so this guide is based on the 17.0→18.0 module diff.
- Manual consumption is now a real choice: you can enable or disable it per BoM line, even for tracked components. It is no longer forced by lot/serial tracking or by an operation.
- Operation dependencies: new "Operation Dependencies" option on BoMs lets you declare which operations must finish before another can start.
- Documents are unified: the separate MRP document model disappears. Files are managed as product documents and can be flagged "Bill of Materials" to appear on BoMs and manufacturing orders.
- BoM lead times: manufacturing lead time and days to prepare the MO are now documented on the BoM.
- Variant support extends to operations and by-products, not only BoM lines.
- Work centers gain tags and product capacities.
- Manufacturing orders gain unbuild links.
- BoM safety: cycle detection blocks a product from containing itself through sub-BoMs; kit BoMs cannot be created for products with reordering rules; by-product cost shares are validated (positive, total ≤ 100).

## Technical data model changes
- Removed model: mrp.document. Replaced by product.document plus attached_on_mrp selection (Hidden / Bill of Materials).
- stock.production.lot renamed to stock.lot; MRP imports and relations updated.
- mrp.bom: added possible_product_template_attribute_value_ids, allow_operation_dependencies, produce_delay, days_to_prepare_mo; product_tmpl_id/product_id now stored/indexed; product_qty uses "Product Unit of Measure"; ready_to_produce default is all_available; inherits product.catalog.mixin; _order = sequence, id; display name uses _compute_display_name; _rec_names_search replaces custom _name_search.
- mrp.routing.workcenter: added blocked_by_operation_ids / needed_by_operation_ids and variant attribute value fields.
- mrp.workorder: added blocked_by_workorder_ids / needed_by_workorder_ids; next_work_order_id removed.
- mrp.workcenter: added tag_ids and capacity_ids.
- mrp.production: added unbuild_ids; removed use_auto_consume_components_lots; date fields adjusted.
- mrp.bom.line: manual_consumption is no longer computed; manual_consumption_readonly removed.
- stock.picking.type: use_auto_consume_components_lots removed.
- Removed fields include module_mrp_workorder, group_locked_by_default, allowed_product_ids, allowed_mo_ids, order_finished_lot_ids.
- Method changes: _bom_find now takes a product recordset and returns a BoM per product; _bom_find_domain takes products; _set_qty_producing has a pick_manual_consumption_moves argument; _determine_is_manual_consumption is based only on the BoM line.
- BoM copy now remaps operation dependencies; BOM explode uses the new finder and cycle checks.

## How your habits should change
- Review every BoM line with tracked components: manual consumption is no longer automatic. If you relied on scanning lots/serials for each tracked component, tick "Manual Consumption"; if you want speed, leave it off and let reserved lots/serials be consumed.
- Check BoM readiness settings: new BoMs default to "When all components are available" instead of "When components for 1st operation are available".
- Stop using standalone MRP documents. Upload files on the product and mark them visible on BoMs; they will show in the BoM/MO chatter.
- Use operation dependencies only when operations must be sequenced; otherwise leave them off for simultaneous scheduling.
- Update reports, imports, and integrations that reference mrp.document, stock.production.lot, use_auto_consume_components_lots, manual_consumption_readonly, or next_work_order_id.
- Expect validation messages for BoM cycles, kit orderpoints, and by-product cost shares.

## What you gain by migrating
- More accurate consumption: decide per component whether operators must scan lots/serials.
- Better planning: lead times and operation dependencies improve scheduling of multi-level BoMs.
- Cleaner document management: one product document repository, visible where production users need it.
- Stronger BoM control: variant-specific operations/by-products, cycle prevention, and cost-share checks reduce data errors.
- Richer work centers: tags and capacities support more precise routing and planning.
- Clearer traceability: unbuilds linked to manufacturing orders.
