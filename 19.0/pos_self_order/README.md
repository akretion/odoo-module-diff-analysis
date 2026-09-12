# pos_self_order migration guide (18.0 -> 19.0)

_Scope: the Community addon `pos_self_order` (QR menu, QR ordering, Kiosk)._

## What's new for users

The 19.0 release notes cover Point of Sale at a high level; only these points really touch self-ordering:

- **PoS presets** — create predefined presets that apply an order schedule and service mode at once (delivery, pick-up, and more). In self-order, presets are what replaces the old "Self Takeaway" flag: the guest chooses *how and where* the order is served instead of ticking a takeaway box.
- **Allergen management for self-orders** is mentioned in the Restaurant part of the notes. It belongs to the Restaurant flow, so confirm availability in your edition before advertising it.

The remaining release note items (ZPL formats, global invoice, product grouping, minimal rights profile, GoFood/GrabFood, one-click payment, due accounts, etc.) concern the cashier POS or are Enterprise-only and are **not** part of this addon. Most of the real work on `pos_self_order` shipped as quality fixes without dedicated release notes: kiosk layout and images, language selection, payment safety, receipt by email, QR codes.

## Technical data model changes

**Removed**

- `pos.config.self_ordering_takeaway` ("Self Takeaway") and `res.config.settings.pos_self_ordering_takeaway`: takeaway is now expressed through presets.
- The dedicated self-order loader on `account.fiscal.position` (now handled by the shared POS loader).
- `pos.order.sync_from_ui()` override (no longer needed once takeaway lives on presets).
- `pos.session.create()` / `_create_pos_self_sessions_sequence()` overrides (order reference reworked in `point_of_sale`).

**Added / changed**

- `pos.preset`: new `available_in_self` (boolean) and `service_at` (Pickup zone / Table / Delivery), plus `mail_template_id`; only presets flagged for self-order (and the default preset) are sent to kiosk/QR.
- `pos.config`: `_prepare_self_order_splash_screen(vals_list, is_new=False)`, plus new helpers `_ensure_public_attachments()`, `_generate_single_qr_code(url)`, `get_pos_qr_order_data()`, `has_valid_self_payment_method()`, `_supported_kiosk_payment_terminal()`.
- `pos.order`: new `action_pos_order_cancel()`, `action_send_self_order_receipt(email, mail_template_id, ticket_image, basic_image)`, `_send_payment_result(payment_result)`; `_process_saved_order(draft)` override dropped.
- `product.template`: `_compute_self_order_visible()` (hides the self-order field when irrelevant), `_process_pos_self_ui_products()`; `product.template` / `product.product` also expose `_can_return_content()`.
- **Unified loading API**: `_load_pos_self_data_domain(data, config)` and `_load_pos_self_data_fields(config)` now take the config *record*; new hooks `_load_pos_self_data_search_read()`, `_load_pos_self_data_read()`, `_load_pos_data_read()`, `_load_pos_data_models()` replace the old `_load_pos_self_data()` / `_post_read_pos_self_data()` pattern. Custom or OCA modules overriding the old methods must be ported.
- `ir.http.get_nearest_lang()` (language selection), `res.config.settings._generate_excel(rows, headers)` (table URLs to XLSX).
- Odoo-wide: `write()` now takes `vals` instead of `vals_list`.

## How your habits should change

- **Retire the Self Takeaway setting.** Configure presets instead (service at = counter / table / delivery) and flag those available in self-order; the default preset is applied automatically.
- The Self Ordering settings block keeps mode, service mode and default language — only the takeaway toggle disappears.
- Re-test kiosk and QR devices end to end: splash/background images, kiosk layout, language selection and payment flows were all reworked.
- Have your integrator review any custom code that extended the self-order data loading or `product.product`/`product.template` write methods.

## What you gain by migrating

- **One loading pipeline for POS and self-order**, removing the "works in the cashier, fails in the kiosk" class of bugs; receipts print reliably because session data is loaded consistently.
- Correct catalogue: unavailable products are not pushed to guests, archived variants are handled.
- Safer payments: orders are not sent to preparation when payment fails; payment results are reported back.
- Better guest experience: improved kiosk responsiveness and image loading, working language selection, receipt by email from self-order, QR code generation for tables, XLSX export of table URLs.
- Smaller technical debt: fewer duplicated overrides to maintain across upgrades.
