# point_of_sale migration guide (13.0 -> 14.0)

## What's new for users

The main change in this addon concerns **stock**: how Point of Sale orders reach Inventory.

* **Stock transfer at session closing (new default).** In 13.0 each POS order created its own transfer as soon as it was synchronised. In 14.0 a **single stock transfer is generated when the session is closed** — matching the release note "Stock: Generate a single stock transfer when closing your PoS session".
* **New setting "Update quantities in stock"** with two choices:
  * *At the session closing (advised)*: one transfer for the whole session (default);
  * *In real time*: the 13.0 behaviour, one transfer per order.
* **Returns are separated**: positive lines and refund (negative) lines now go to two distinct transfers instead of one document.
* The PoS **Operation Type** (picking type) is now mandatory in the configuration.

Other Point of Sale items listed in the official 14.0 notes (mobile interface, cash rounding, product configurator, camera barcode scanning, payment screen) are not covered by the data-model changes analysed here; ask your integrator whether they apply to you.

## Technical data model changes

**stock.picking** (new extension)
* New fields `pos_session_id` (→ pos.session) and `pos_order_id` (→ pos.order).
* New methods `_create_picking_from_pos_order_lines()` and `_create_move_from_pos_order_lines()`; picking/move logic moved here from pos.order.

**pos.order**
* `picking_id` (Many2one) **removed** → `picking_ids` (One2many on stock.picking.pos_order_id): an order can now have several transfers.
* `location_id` **removed** (was related to `picking_id.location_id`).
* Added computed fields `picking_count` and `failed_pickings`, and `action_stock_picking()` for the smart button.
* Removed methods `create_picking()`, `_force_picking_done()`, `set_pack_operation_lot()`; added `_create_order_picking()`.
* `action_pos_order_paid()` no longer creates the picking.

**pos.session**
* New `update_stock_at_closing` (Boolean), initialised from the company setting.
* New `picking_ids` (One2many on stock.picking.pos_session_id); `picking_count` and the new `failed_pickings` compute from it.
* `_validate_session()` now creates the session pickings via `_create_picking_at_end_of_session()`.

**pos.config**
* `picking_type_id` is now required.
* `use_existing_lots` **removed** (lot rules are read from the operation type).

**res.company / res.config.settings**
* New selection `point_of_sale_update_stock_quantities` (`closing` by default, or `real`), exposed as `update_stock_quantities`.

**account.move**: stock lookups now use `pos_order_ids.picking_ids`; the sign of `_get_pos_anglo_saxon_price_unit()` was corrected.
**stock.warehouse**: POS operation type created with `show_operations = False`.

## How your habits should change

* With the default setting, **nothing is posted to stock before the session is closed** — close your sessions regularly. Switch to *In real time* if you need live availability.
* Always set an operation type on each POS configuration.
* Don't look for lot/serial rules on the POS config anymore: set them on the operation type.
* Instead of a single "Picking" field, use the transfers smart button (`picking_count` / `failed_pickings`) to check an order or a session.

## What you gain by migrating

* **Much faster synchronisation** on busy shops: no more one picking per order.
* **Cleaner Inventory**: one transfer per session and destination, plus a dedicated return transfer for refunds.
* **Flexibility**: real-time or deferred stock, per shop, with one setting.
* **Better follow-up**: failure indicators show which transfers did not complete, on both orders and sessions.
* A stock flow aligned with the 14.0 Inventory improvements (forecasted inventory, valuation report, simplified transfers).
