# point_of_sale migration guide (18.0 -> 19.0)

## What's new for users

Items from the Odoo 19.0 release notes that really concern this addon:

- Group products by parent and child categories in the POS terminal.
- Product info is opened by long press/click on the product card, or from the action button.
- PoS presets: predefined presets apply a mode and a schedule to an order (delivery, pickup, ...). Scheduling is now based on working calendars: working periods per weekday plus an interval, instead of opening/closing hours.
- Localizations touching the POS: Brazil (batch NFC-e, XML export), Kenya (eTIMS), Malaysia (e-invoice from the session and consolidated invoices), Peru (SUNAT thermal printing), Saudi Arabia (ZATCA Phase 2 QR on receipts), Hong Kong (QFPay terminal).

Restaurant courses/allergens, preparation display, default ZPL formats, global invoices and employee-rights profiles belong to other, mostly Enterprise, apps; check your subscription before relying on them.

## Technical data model changes

- POS product cards now load `product.template` (with `product.product`, attributes and pricelists as complements) instead of `product.product` only. Limited-product loading now runs on templates.
- `product.packaging` is removed from POS and replaced by `product.uom` (barcode, product_id, uom_id): packagings are merged into Units of Measure.
- `pos.order` amounts (amount_difference, amount_tax, amount_total, amount_paid, amount_return, tip_amount) and `pos.order.line` price_subtotal/price_subtotal_incl become Monetary fields, rounded on the session currency.
- `pos.order.pos_reference` now follows YYLL-SSS-FOOOO; `tracking_number` becomes a stored, readonly field (no longer computed nor searched through the reference); `sequence_number` is negative when the order was created offline.
- `pos.session`: `sequence_number` and `login_number` are removed, replaced by `order_seq_id` and `login_number_seq_id` (ir.sequence records created per session and deleted with it).
- `pos.order.session_id` is no longer required; `config_id` is now computed from the session (still stored).
- `pos.order.procurement_group_id` is replaced by `stock_reference_ids`: procurement.group is replaced by stock.reference in Inventory, and stock moves/pickings link to orders through references.
- `pos.preset`: hour_opening, hour_closing, capacity_per_x_minutes, x_minutes, preparation_time_minute and address_on_ticket are removed; identification, is_return, image_128, resource_calendar_id, attendance_ids, slots_per_interval, interval_time, count_linked_orders and count_linked_config are added, with resource.calendar.attendance loaded in POS.
- `pos.config`: orderlines_sequence_in_cart_by_category is removed, and the Preparation Display toggle left the POS settings.
- POS pricelist items are now filtered on their validity dates (date_start/date_end).
- Helpers `_get_available_categories`, `_get_available_product_domain` and `get_limited_products_loading` are removed.

## How your habits should change

- Packagings are now Units of Measure: create a UoM with the right name and ratio and add it to the product's units; existing packaging records must be migrated.
- Order numbers look different: year + device + session + sequence. Offline orders are marked F=1 with a negative sequence number, which is expected.
- Presets: replace opening/closing hours with calendar working periods and an interval; slots are generated for the next 7 days.
- A preset order scheduled in the future cannot be cancelled from the backend: it is detached from its session instead.
- If you limit categories on a POS config, products of child categories now appear automatically.
- Amounts now display with your currency rounding everywhere (lists, exports, receipts).

## What you gain by migrating

- Better performance and no more variant/pricelist workarounds in POS, thanks to template-based product loading.
- Reliable per-session sequential order numbering, with device identification and explicit marking of offline orders; session counters become sequences, avoiding concurrent-update errors.
- Correct currency rounding on all POS amounts.
- One single unit concept (Units of Measure) shared with Sales, Purchase and Inventory.
- Calendar-based presets for cleaner pickup/delivery slots.
- Up-to-date POS e-invoicing compliance for several localizations.
