# stock migration guide (15.0 -> 16.0)

Scope: the Community `stock` addon (Inventory). Between 15.0 and 16.0 it was mostly renamed, not redesigned: no model was added or removed and no method signature changed. The impact is on field names, so imports, filters, reports and custom code must be adapted.

## What's new for users

From the official 16.0 release notes, the items concerning the Inventory/stock functionality:

- **Force backorders**: each operation type can decide whether backorders are created automatically, instead of relying on the validation prompt.
- **Transfers**: the done quantity can be edited directly in the transfer operations.
- **Inventory adjustments**: last count date, warning icon next to duplicated serial numbers, starred-products filter and an "Apply all" button.
- **Reception report**: improved, with links to the related Sales Orders, product names on labels, and its auto-popup setting moved to the operation type.
- **Lots and packages labels**: GS1-128 labels for lots and serial numbers (product, lot/SN, expiry and sell-to dates); printing a package content also prints datamatrix codes for its content.
- **Replenishment**: visibility days on replenishment rules to consider needs after the forecast date; available stock of other warehouses shown in the replenishment report.
- **Reporting and menus**: all stock reports and menus were reworked for easier navigation.

Other release-note entries (eCommerce, Manufacturing, Barcode mobile app) belong to other addons and are out of scope here.

## Technical data model changes

Two field renames, both breaking. No model and no method signature change was detected.

**1. `stock.picking.move_lines` -> `stock.picking.move_ids`**
The One2many of "Stock Moves" keeps the same label and meaning. Everything built on it follows: related fields `group_id` and `product_id`, delay-alert search, onchanges and computes.

**2. `stock.move.line` reservation fields renamed**
On operation lines, the two fields holding the reservation are renamed to stop confusing them with the demand of the move (`stock.move.product_uom_qty` / `product_qty` are unchanged):
- `product_uom_qty` -> `reserved_uom_qty` ("Reserved", line UoM);
- `product_qty` -> `reserved_qty` ("Real Reserved Quantity", product base UoM, stored computed).

The helper methods `_compute_product_qty` / `_set_product_qty` became `_compute_reserved_q
