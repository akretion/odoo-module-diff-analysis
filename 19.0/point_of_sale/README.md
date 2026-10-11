# point_of_sale migration guide (18.0 -> 19.0)

## What's new for users

- **PoS presets reworked.** Presets now rely on a *Resource calendar*: you define weekly attendances (opening/closing per day) and the terminal offers bookable slots for the next seven days, with a configurable interval and capacity per slot. Presets also gain an identification requirement (none / address / name), a return mode, a colour, an image, and links to their orders and POS configurations.
- **Products now rely on product templates.** Variants, attributes and pricelists are handled natively on the product card instead of being proxied through the variant.
- **Product grouping and product info.** Products can be grouped by parent and child categories in the terminal; product information is available via long press/click or the action button, and payments can be validated in one click.
- **New receipt numbering.** Orders get a fully sequential receipt reference per POS configuration, and each terminal is identified by a *device identifier* instead of a login counter.

## Technical data model changes

- `pos.config`: removed `sequence_id`, `sequence_line_id` and the helpers `_get_available_categories()`, `_get_available_product_domain()`, `get_limited_products_loading()`; added `order_seq_id`, `order_backend_seq_id`, `order_line_seq_id`, `device_seq_id`. Server-side data (`_server_version`, `_base_url`, `_data_server_date`, `_has_cash_move_perm`, `_pos_special_products_ids`, `_product_default_values`, `_IS_VAT`) is now served by `pos.config` instead of `pos.session`.
- `pos.session`: removed `order_seq_id`, `login_number_seq_id`, `login()` and `get_next_order_refs()`; `_load_pos_data_models()` / `_load_pos_data_fields()` now receive a config record rather than an id.
- `pos.order`: `session_id` is no longer required and `config_id` is computed from it; `procurement_group_id` is replaced by `stock_reference_ids`; new `source` field; `tracking_number` is now stored and readonly; the amount fields (`amount_total`, `amount_tax`, `amount_paid`, `amount_return`, `amount_difference`, `tip_amount`, `price_subtotal`, `price_subtotal_incl`) move from Float to Monetary.
- `pos.preset`: removed `address_on_ticket`, `hour_opening`, `hour_closing`, `capacity_per_x_minutes`, `x_minutes`, `preparation_time_minute`; added `identification`, `is_return`, `image_128`, `resource_calendar_id`, `attendance_ids`, `slots_per_interval`, `interval_time`, plus order/config counters. `resource.calendar.attendance` is now loaded in the PoS.
- `product.packaging` is removed (merged into units of measure); `product.uom` is loaded in the PoS and used for packaging barcodes.
- `pos.load.mixin`: `_load_pos_data()` and `_post_read_pos_data()` are replaced by `_load_pos_data_search_read(data, config)`, `_load_pos_data_domain(data, config)`, `_load_pos_data_fields(config)` and `_load_pos_data_read(records, config)`. `ir.module.module` now uses the mixin.
- Pricelist items are loaded only when inside their validity dates; POS category filtering now uses the descendants of the allowed categories.

## How your habits should change

- **Receipt numbers look different**: they are no longer session-based (`YYLL-SSS-FOOOO`); each configuration now numbers its orders sequentially and includes a device identifier.
- **Sequences are automatic**: there is nothing to edit on the POS configuration anymore.
- **Invoicing from the POS now requires an invoice journal** on the configuration, otherwise Odoo raises "No invoice journal configured for this POS session."
- **Packagings are units of measure**: create an extra UoM on the product instead of a packaging; scanning its barcode still works.
- **Preset opening hours** are configured on the linked Resource calendar (attendances), not as opening/closing hour fields.
- The "Order cart by category's sequence" option has been removed.
- Orders booked for a future date cannot be cancelled from the list; they are detached from the session instead.
- Custom modules reading `procurement_group_id` or `product.packaging` must be adapted.

## What you gain by migrating

- Faster, more reliable loading: product-template based data, date-filtered pricelists and one unified loading pipeline.
- Gap-controlled numbering per configuration and per device, easier to reconcile with a till or a kiosk.
- Currency-safe amounts everywhere (monetary fields) instead of raw floats.
- Much richer scheduling for pick-up/delivery presets, based on real opening hours.
- A simplified unit model: no more duplicate packaging / UoM concepts.
- A supported version with up-to-date localizations, payment terminals and receipt formats.
