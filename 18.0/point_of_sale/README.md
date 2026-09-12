# point_of_sale migration guide (17.0 -> 18.0)

Summary of what changes in the Point of Sale app between Odoo 17.0 and 18.0, for users and for the underlying data model.

## What's new for users

- **Redesigned POS screen**: the front end has been rebuilt with a simpler, more touch-friendly layout.
- **Customer display without an IoT box**: the checkout screen is now a standalone app that can run on any remote device. Settings offer a single "Customer Facing Display" choice (same device / another device / IoT-connected screen) plus a background image.
- **Editable payments**: payment methods can be corrected before the receipt is printed.
- **QR code payments**: a QR payment method lets customers pay from their banking application.
- **Cash difference logging**: opening and closing cash counts, expected amount, difference and notes are always recorded on the session.
- **Create products in PoS**: products can be created and edited directly from the POS interface.
- **One order at a time for refunds**: products from a single order only can be refunded in one refund.

(The 18.0 release notes also list restaurant novelties such as predefined internal notes and table merge; those belong to the separate `pos_restaurant` app, not to this module.)

## Technical data model changes

**pos.order**
- Removed: `is_refunded`, `refunded_order_ids` (Many2many), `refunded_orders_count`.
- Added: `refunded_order_id` (Many2one) — "Order from which items were refunded in this order"; `refund_orders_count` now counts refund orders.
- `create_from_ui()` → `sync_from_ui()`; `_process_order(order, draft, existing_order)` → `_process_order(order, existing_order)`.
- `action_receipt_to_customer()` → `action_send_receipt(email, ticket_image, basic_image)`; `action_view_refunded_orders()` → `action_view_refunded_order()` (form) plus `action_view_refund_orders()`.
- New tracking helpers for order control and payment changes (`_compute_is_edited`, `_create_pm_change_log`, `_post_chatter_message`).

**pos.config**
- Removed: `iface_customer_facing_display`, `..._via_proxy`, `..._local`, `..._background_image_1920`.
- Added: `customer_display_type` (none / local / remote / proxy), `customer_display_bg_img`, `customer_display_bg_img_name`.
- New: `update_customer_display(order, access_token)`, `_get_customer_display_data()`, `_get_display_device_ip()`, `set_opening_control()`, `delete_opening_control_session()`, `post_close_register_message()`.

**New mixins**
- `pos.bus.mixin`: brings `access_token` and `_notify()` to `pos.config`, `pos.session` and `pos.order`; the old `access_token` field and `_get_bus_channel_name()` on sessions disappear.
- `pos.load.mixin`: standardises POS data loading with `_load_pos_data`, `_load_pos_data_fields`, `_load_pos_data_domain`, `_load_pos_data_models`, `_load_pos_data_relations`. It replaces the `_loader_params_*` / `_get_pos_ui_*` methods removed from `pos.session`, and is implemented on many standard models (products, partners, taxes, fiscal positions, journals, UoMs, picking types, …).

**Other models**
- `pos.payment`: `_create_payment_moves(is_reverse=False)`, `_check_amount()`.
- `pos.payment.method`: QR support (`_compute_qr`, `_compute_hide_qr_code_method`, `get_qr_code()`, `_onchange_payment_method_type()`).
- `pos.order.line`: `_get_discount_amount()`, `_prepare_tax_base_line_values()`, `get_existing_lots()`, `unlink()`.
- `pos.session`: `find_product_by_barcode(barcode, config_id)`, `_get_sale_vals(key, sale_vals)`, `_post_cash_details_message(state, expected, difference, notes)`, `load_data(models_to_load, only_data)`.
- Combo logic moved to the product module (`product.combo`, `product.combo.item`); matching `pos.combo` methods were removed.
- Token-based access to product images for the customer display (`ir.binary`); product-level journal entries at session closing; amounts adjusted for reversed POS orders.

## How your habits should change

- **Customer display setup**: forget the two checkboxes of 17.0 — pick one display type and upload the background image. Remote screens no longer need an IoT box, but each screen is opened separately.
- **Refunds**: you no longer mix lines from several orders in one refund. The refund points to a single source order through `refunded_order_id`, and the smart button opens that order.
- **Start category**: the "start category" configuration was removed; the POS opens on the available categories (use "Available PoS Product Categories" to restrict the list).
- **Cash counts**: opening and closing differences are systematically logged on the session, so expect a trace even when counts match.
- **Payments**: review the payment screen before printing — you can now fix or add payment lines at that stage.

## What you gain by migrating

