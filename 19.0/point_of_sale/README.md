# point_of_sale migration guide (18.0 -> 19.0)

## What's new for users
- **Group products** by parent and child categories in the terminal: a POS limited to a parent category now also loads its sub-categories.
- **Product info**: long press/click a product card, or use the product action button, to display product information.
- **One-click payment validation**: validate a payment with a single click.
- **Presets**: predefined presets apply order modes and schedules quickly (delivery, pick-up, ...). They now use a weekly working schedule, can require identification (none / address / name), can run in return mode, can carry an image, and expose capacity and slot intervals for the next 7 days.
- **Localizations** (separate modules, check availability): Peru SUNAT thermal printing, Kenya eTIMS, Malaysia consolidated e-invoices, Brazil NFC-e batches, Saudi ZATCA Phase 2 QR on receipts.
- The official 19.0 notes list few other Community POS items; most POS features there are Enterprise or localization modules.

## Technical data model changes
- **Product cards**: POS loads `product.template` as the product model (previously `product.product`); variants, attributes and pricelists are handled on the template. `pos.config._get_available_categories`, `_get_available_product_domain` and `get_limited_products_loading` are removed; pricelists loaded in the POS are filtered on their start/end dates.
- **Packagings**: `product.packaging` no longer exists (merged into units of measure). The POS loads `product.uom` (barcode, product_id, uom_id); barcode scanning returns `product.uom`.
- **Sequences / receipt numbers**: `pos_reference` becomes `YY-POSID-SEQUENCE` (e.g. `000-0-000000`); `tracking_number` is a stored Char (no computed value, no custom search); `sequence_number` can be negative for offline orders. Sequences move from session to config: new `order_seq_id`, `order_backend_seq_id`, `order_line_seq_id`, `device_seq_id`. Removed: `pos.config.sequence_id` / `sequence_line_id`, `pos.session.order_seq_id` / `login_number_seq_id`; `login_number` is replaced by a device identifier.
- **Orders**: `session_id` is no longer required, `config_id` is a stored computed field derived from the session, and a new `source` field is added. Future-dated preset orders can no longer be cancelled.
- **Presets**: added `identification`, `is_return`, `image_128`, `resource_calendar_id`, `attendance_ids`, `slots_per_interval`, `interval_time` and linked-count fields; removed `address_on_ticket`, `hour_opening`, `hour_closing`, `capacity_per_x_minutes`, `x_minutes`, `preparation_time_minute`. `resource.calendar.attendance` is now loaded in the POS.
- **Amounts**: order and order-line amounts (`amount_total`, `amount_tax`, `amount_paid`, `amount_return`, `amount_difference`, `tip_amount`, `price_subtotal`, `price_subtotal_incl`) are now Monetary (currency-aware) instead of plain floats.
- **Stock**: `procurement.group` is replaced by `stock.reference`; `pos.order.procurement_group_id` becomes `stock_reference_ids` (many2many), with `stock.reference.pos_order_ids`.
- **Loading API**: hooks become `_load_pos_data_search_read(data, config)`, `_load_pos_data_read(records, config)`, `_load_pos_data_domain(data, config)`, `_load_pos_data_fields(config)` and `_unrelevant_records(config)`; `_load_pos_data`, `_post_read_pos_data` and `_read_pos_record` are removed. Session-level values (server version, base URL, permissions) are now read from `pos.config`. `orderlines_sequence_in_cart_by_category` and its setting are removed.
- Invoicing an order now requires an invoice journal on the POS configuration, otherwise an error is raised.

## How your habits should change
- Use long press/click or the product action button for product info instead of a separate information screen.
- Validate payments in one click.
- Rely on parent/child categories to display and group products.
- Treat presets as schedules: opening hours are weekly attendances, slots cover the next 7 days.

## What you gain by migrating
- A lighter, faster POS: product cards come from the template, and all data loading goes through one consistent API.
- A data model aligned with the rest of Odoo: UoM instead of packagings, `stock.reference` instead of procurement groups, currency-aware amounts.
- Cleaner order numbering: per-POS sequences with separate counters for backend orders, order lines and devices.
- More capable presets for delivery/pick-up flows, plus a quicker checkout (one-click payment, quick product info, category grouping).
