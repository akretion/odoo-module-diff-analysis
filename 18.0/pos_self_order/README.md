# pos_self_order migration guide (17.0 -> 18.0)

Self-order covers the QR menu, QR menu + ordering and Kiosk front ends. 17.0 -> 18.0 is mostly a deep data-loading refactoring, plus a few visible improvements.

## What's new for users

The official 18.0 release notes have no self-order section: the Point of Sale chapter only lists shared features (front-end redesign, cash difference logging, product creation in the PoS). The Enterprise-only items it mentions (AvaTax, IoT customer display, restaurant add-ons) are not part of this Community addon and are not listed below. User-visible changes come from the code:

- Time-based menus: categories can now be filtered by opening hours (breakfast only in the morning).
- Kiosk URL and PWA: "Open kiosk" is replaced by a kiosk URL and an on-demand manifest, so the kiosk page can be installed as an app on a tablet.
- Kiosk onboarding: a scenario is available to load sample self-order data.
- Kiosk payments: cash is removed from the kiosk payment methods.
- Kiosk pricelist: the PoS pricelist is applied/computed again for kiosk mode.
- Takeaway: unified with the main PoS. The setting becomes "Self Takeaway" and uses the PoS takeaway fiscal position, so taxes are correct everywhere.
- Product display: names are shown without the internal reference; the product info button shows correctly.
- Images: product images are now served by the standard Odoo binary controller.

## Technical data model changes

- pos.config: `access_token` is no longer declared here — it comes from the new `pos.bus.mixin`; `_get_access_token`/`_init_access_token` are removed and notifications go through `_notify(...)`. `self_ordering_alternative_fp_id` is removed; `self_ordering_takeaway` becomes "Self Takeaway". `action_open_kiosk` is replaced by `get_kiosk_url`. New: `load_self_data`, `_load_self_data_models`, `load_onboarding_kiosk_scenario`, `_prepare_self_order_custom_btn`/`_splash_screen`, `_onchange_payment_method_ids`.
- pos.order: `take_away` removed (use the core `takeaway` field). `create_from_ui` replaced by `sync_from_ui` + `_process_saved_order`. Removed: `_export_for_self_order`, `_compute_tax_details`, `_order_fields`, `_send_order`, `export_for_ui_table_draft`, `export_for_ui_shared_order`, `get_standalone_self_order`.
- pos.order.line: `combo_parent_id` and `combo_line_ids` removed (combo lines now live in point_of_sale); `combo_id` stays.
- Generic loaders replace bespoke exporters: `_load_pos_self_data`, `_load_pos_self_data_domain` and `_load_pos_self_data_fields` are added on pos.load.mixin, pos.category, product.product, account.fiscal.position, restaurant.floor/table and custom links; pos.session swaps `_loader_params_product_product`/`_pos_data_process` for `_load_pos_data`. Removed on pos.config/product: `_get_self_ordering_data`, `_get_available_categories`, `_get_available_products`, `_get_combos_data`, `_get_self_order_custom_links`, `_get_self_ordering_image`, `_get_product_for_ui`, `_get_price_info`, `_get_attributes`, `_get_self_order_data`.
- Related models now accept arbitrary server data through `_`-prefixed keys.
- pos.payment.method: `payment_request_from_kiosk` -> `_payment_request_from_kiosk` (permissions).
- ir.binary: `_find_record_check_access` added; the custom image controllers are removed.
- res.config.settings: `_compute_pos_pricelist_id` and `_onchange_pos_payment_method_ids` added; `pos_self_ordering_alternative_fp_id` removed.
- product.product: the `_get_name` override is dropped.

## How your habits should change

- Publish the kiosk URL (or install the PWA) instead of using the "Open kiosk" button.
- Configure takeaway once, in the Point of Sale settings, not in a self-order-specific field.
- Do not expect a cash option on the kiosk; products must still be flagged "available in self order".
- For custom code: replace removed `_get_*` exporters with `_load_pos_self_data*` hooks, use `sync_from_ui`, `_notify` and standard image URLs.
- Training on the kiosk screens stays valid; only the staff-side PoS front end was redesigned.

## What you gain by migrating

- Time-based menus and a stable, installable kiosk link (PWA) with ready-made onboarding data.
- One takeaway logic shared with the main PoS, with the right fiscal position and taxes.
- A unified data-loading framework: less custom code, more data on the kiosk, safer image serving.
- Per-configuration bus notifications with dedicated access tokens (less cross-talk between kiosks).
- A supported version with security fixes and the Community 18 PoS improvements.
