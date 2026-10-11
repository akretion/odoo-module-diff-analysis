# pos_sale migration guide (19.0 -> 20.0)

Odoo 20.0 splits the Point of Sale / Sales-order integration: `pos_sale` now links POS orders to sale orders **without** requiring the Inventory (stock) app. All delivery, reservation, picking and lot/serial handling that used to live inside `pos_sale` has been moved to a new bridge module, `pos_sale_stock`.

## What's new for users

- **Use POS without Inventory.** The Point of Sale app can now run with *simplified inventory management*: a single on-hand quantity per product, no transfers, no pickings. This is the headline change of 20.0 for shops whose POS orders are linked to sale orders but who never needed full warehouse operations.
- For these shops, confirming a POS order still confirms the related draft/sent sale orders, but nothing else is created behind the scenes (no delivery order to process, no stock reservation to chase).
- If your business does need deliveries, this behaviour is not lost: the bridge module `pos_sale_stock` restores the previous picking creation, reservation and delivered-quantity logic.
- The official release notes contain no other item specific to `pos_sale`; most Point of Sale novelties published for 20.0 (multiple currencies at checkout, service fees, printer management, self-ordering, delivery-platform integrations, localisation e-invoicing) belong to the `point_of_sale` app itself or to Enterprise/localisation packages and are out of scope here.

## Technical data model changes

**Removed / moved**

- `models/stock_picking.py` is deleted: `pos_sale` no longer inherits `stock.picking` and no longer overrides `_create_move_from_pos_order_lines` (the automatic unreserve of moves when the POS order ships from the same warehouse).
- `pos.order`: the block that updated demand quantities on stock moves and cancelled fully-delivered pickings (and re-reserved the remainder) after confirming sale orders is removed from the order-processing flow.
- `pos.order` no longer overrides `_force_create_picking_real_time`.
- `pos.order.line` no longer overrides `_launch_stock_rule_from_pos_order_lines` (cancelling roll-up moves of the originating sale order).
- `sale.order.line` no longer overrides `_compute_qty_delivered` / `_prepare_qty_delivered`; POS-delivered quantities are no longer added to the delivered quantity computed in `sale`.
- `sale.order._load_pos_data_fields`: the `picking_ids` field is no longer loaded into the POS front-end.

**Changed behaviour**

- `pos.order.line.qty_delivered`: dependencies shrink from order state + pickings + their move quantities to `order_id.state` only. The value is now simply the line quantity when the order is *paid*/*done*, and 0 otherwise; partial deliveries coming from pickings are no longer reflected at this level.

**New extension point**

- `sale.order.line._get_read_converted_extra_items(sale_line)` returns `{}` by default. The lot/serial payload previously built inline (`lot_names`, `lot_qty_by_name`) while converting POS quantities is now injected through this hook, which `pos_sale_stock` implements.

## How your habits should change

- **If you deliver, track or reserve products from POS orders**, install `pos_sale_stock`. Without it, POS orders linked to sale orders will not create or update pickings, and delivered/invoiced quantities on sale order lines will not reflect what the POS actually shipped.
- **If you only sell services, or count stock with the simplified one-quantity-per-product model**, nothing to install and nothing to do: day-to-day cashier and sales-order workflows are unchanged, only behind-the-scenes picking work disappears.
- Technical teams: customisations that hooked into the removed overrides (`stock.picking._create_move_from_pos_order_lines`, `_force_create_picking_real_time`, `_launch_stock_rule_from_pos_order_lines`) or into `SaleOrderLine._prepare_qty_delivered` must be re-targeted to `pos_sale_stock`, or moved onto the new `_get_read_converted_extra_items` hook.
- Anything reading `picking_ids` from the POS data payload must be updated, as that field is no longer sent to the front-end.

## What you gain by migrating

- **A lighter, cheaper deployment.** Smaller shops can run Sales + Point of Sale without the Inventory app, without transfers, and without warehouse configuration to maintain.
- **Clear separation of concerns.** Stock complexity is opt-in through `pos_sale_stock`; upgrades and custom developments touch a smaller, more focused module.
- **Cleaner extension hooks.** Lot/serial and converted-quantity extras are now provided through a documented override point instead of hard-coded logic inside `pos_sale`.
