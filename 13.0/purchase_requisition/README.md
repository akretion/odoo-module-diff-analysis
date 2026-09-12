# purchase_requisition migration guide (12.0 -> 13.0)

## What's new for users

The official 13.0 release notes contain **no entry specific to purchase requisitions** (call for tenders, blanket orders / Purchase Agreements). Nothing there describes a change to the requisition screens, workflow or documents.

The only Purchase-app items in the notes are generic and apply to the Purchase app rather than to this addon: adding sections and notes to purchase orders, a new purchase dashboard, and creating product variants in batch from an attribute grid. These are Community features, but they are not requisition features — they do not change how tenders work. The other chapters (Accounting, eCommerce, Approvals, SEPA, ...) are out of scope here, and several are Enterprise-only, so do not assume they come with a Community migration.

The real news for requisitions comes from the code: a requisition is no longer tied to Inventory.

## Technical data model changes

From the 12.0 -> 13.0 module diff (2 commits, ~31 KB):

**1. Requisitions made service-friendly (stock split out)**

- `purchase.requisition`: removed `warehouse_id` (stock.warehouse) and `picking_type_id` (stock.picking.type, "Operation Type", required, defaulting to the incoming operation type); helper `_get_picking_in()` deleted.
- `purchase.requisition.line`: removed `move_dest_id` (stock.move, "Downstream Move"); the line no longer forwards `move_dest_ids` when preparing purchase order lines.
- Stock overrides removed from this addon: `procurement.group`, `stock.rule` (automatic tender creation from a procurement), `stock.move` (`requisition_line_ids`, upstream document tracking) and `stock.warehouse.orderpoint` (quantity in progress).
- All of this moved to the new companion module **`purchase_requisition_stock`**, together with the related access rights. It keeps the "requisition from stock demand / availability" behaviour when Inventory is used.
- Consequence: a purchase order generated from a requisition no longer copies an operation type from the requisition; it uses the normal Purchase Order default.

**2. Code split into files (no functional change)**

Models were moved into dedicated files: purchase (`purchase.order`, `purchase.order.line`), product (`product.template.purchase_requisition`, `product.product._prepare_sellers`, `product.supplierinfo`), requisition and stock. Fields such as `requisition_id`, `is_quantity_copy`, `purchase_requisition_id` and `purchase_requisition_line_id` keep the same names and behaviour. No method signature changes were detected.

## How your habits should change

- The requisition form no longer shows Warehouse and Operation Type: less to fill in, and nothing to check for a service purchase.
- If you drive requisitions from stock (reordering rules, procurements), make sure the companion module `purchase_requisition_stock` is installed alongside Inventory — that behaviour lives there now.
- For service-only or blanket-order usage, you can install `purchase_requisition` on its own, without Inventory.
- Purchase orders created from a requisition take the standard Purchase Order operation type, not one set on the requisition.
- Everything else (tender types, quantity/price copy, exclusive tenders, vendor info on requisition lines, chatter and activities) works as before.

## What you gain by migrating

- A leaner Purchase Requisition app that works for service companies, not only for stocked products.
- Stock-driven requisitions remain available through `purchase_requisition_stock`: same features, better separated.
- Cleaner, more maintainable code (split models), meaning faster and safer future upgrades and customisations.
- You move to a supported 13.0 Community branch, keeping mail tracking and activities on requisitions.
- Plus the general Purchase gains of 13.0 (sections and notes on purchase orders, purchase dashboard) once your tenders become purchase orders.

Migration effort is mostly structural: check custom reports, exports, automations or custom code that referenced `warehouse_id`, `picking_type_id` or `move_dest_id` on requisitions or requisition lines.
