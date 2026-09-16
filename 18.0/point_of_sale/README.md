# point_of_sale migration guide (17.0 -> 18.0)

## What's new for users

- **Redesigned PoS interface**: the front end has been fully reworked for simplicity and speed.
- **Create products in PoS**: create and edit products and their variants directly from the Point of Sale app.
- **Customer display without an IoT box**: the display is now a standalone app that runs on any remote device on the network (tablet, PC). Setup is a single "Customer Facing Display" option — None / Same device / Another device / IoT-connected screen — plus a background image.
- **QR code payments**: customers can pay by scanning the QR code with their bank application.
- **Editable payments**: payments can be adjusted before the receipt is printed.
- **Cash difference logging**: opening and closing cash counts are always recorded on the PoS session.
- **Localization addons** (separate modules): JoFotara receipts (Jordan), on-demand ZATCA PDF (Saudi Arabia), TicketBAI (Spain), SInvoice e-invoices (Vietnam), QRIS status (Indonesia), EDI with POS orders (Peru).

## Technical data model changes

- **New `pos.load.mixin`**: data sent to the PoS client is now declared per model through `_load_pos_data_domain()`, `_load_pos_data_fields()` and `_load_pos_data()`. `pos.session._load_data_params()` was removed and replaced by `_load_pos_data_models()`, which lists the loaded models. Nearly all POS models now inherit the mixin (pos.order and lines, payments and payment methods, printers, categories, bills, notes, taxes, cash rounding, fiscal positions, UoM, partners/companies/currencies/countries/languages, products, picking types, views). Custom server values can be sent using keys prefixed with `_`.
- **New `pos.bus.mixin`** (`access_token` field, `_notify()` method), inherited by pos.config, pos.session and pos.order. The token field left `pos.session`; `_ensure_access_token()` and `_get_bus_channel_name()` were removed.
- **Customer display fields replaced** on `pos.config`: `iface_customer_facing_display` (and its local / via-proxy / background-image variants) removed; added `customer_display_type`, `customer_display_bg_img`, `customer_display_bg_img_name`, plus `update_customer_display()` and `_get_customer_display_data()`.
- **Combos moved to the Product module**: `pos.combo` becomes `product.combo`, `pos.combo.line` becomes `product.combo.item`, `combo_line_ids` becomes `combo_item_ids`, `pos.order.line.combo_line_id` becomes `combo_item_id`, and the extra-price field is renamed `extra_price`.
- **Refund fields simplified**: `is_refunded`, `refunded_order_ids` and `refunded_orders_count` removed; `refunded_order_id` (single order) added. Actions renamed to `action_view_refunded_order` / `action_view_refund_orders`.
- **Start Category removed**: `pos.config.iface_start_categ_id` and `start_category` deleted, together with their settings fields and constraint.
- **Amounts are Monetary now**: `amount_total`, `amount_tax`, `amount_paid`, `amount_return`, `amount_difference`, `tip_amount` on orders, and `price_subtotal` / `price_subtotal_incl` on order lines. Decimals follow the session currency.

## How your habits should change

- Start Category is gone: use Restrict Categories to choose which categories appear in PoS.
- Refund one order at a time: lines from several orders can no longer be grouped in a single refund; each refund is linked to one original order.
- Combos are configured on the product (type "Combo") and shared with Sales and eCommerce; "Combo Lines" are now "Combo Items".
- Customer display setup: pick a device type instead of ticking separate proxy/local options.
- Custom developments must move from `_load_data_params` to the new `pos.load.mixin` hooks, and use `pos.bus.mixin` for tokens and bus notifications.
- Amount fields keep their names but are currency-aware: review any integration that assumed fixed zero decimals.

## What you gain by migrating

- A modern, faster till interface with fewer clicks for cashiers.
- Customer displays on ordinary devices, with no IoT box to buy or maintain.
- Combos usable consistently in Sales, eCommerce and the Point of Sale.
- Refunds that follow accounting logic: one source order, one refund, correct reverse invoices.
- In-store product creation, QR payments and broader fiscal compliance for POS orders.
- A cleaner, per-model data-loading API, making future customizations easier to maintain.
