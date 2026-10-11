# pos_self_order migration guide (19.0 -> 20.0)

This addon provides the customer-facing self-ordering flow (kiosk and mobile) of the Point of Sale. Between 19.0 and 20.0 its server-side data loading was reworked, and a set of new self-ordering features was released. This guide summarises what a functional user will notice and what a developer must adapt.

## What's new for users

Taken from the official 20.0 release notes, the "Point of Sale > Self-ordering" section is the only part that concerns this addon:

- **Notes at checkout**: customers using self-ordering can leave notes at checkout; these are visible on the kitchen display.
- **Optional products on mobile and kiosk**: optional products are now displayed when ordering from a mobile device or a kiosk.
- **Order-specific QR code**: generate and print a QR code per order so customers can scan it to place their order and pay after their meal.
- **Snooze products**: make products temporarily unavailable on the POS or self-ordering menu by snoozing them.
- **Service fees via presets**: add a service fee to Point of Sale orders and self-orders through presets, with the amount displayed on invoices.
- **Receipt by WhatsApp or SMS**: customers can receive their self-order receipt through WhatsApp or SMS.

Other 19.0/20.0 Point of Sale notes (printer management, payment terminals, delivery marketplaces, multiple currencies) belong to other modules and are out of scope here. The release notes contain no other item describing the self-order data loading rework below — that information comes from the code changes only.

## Technical data model changes

**Models and fields**

- No field is added, removed or renamed on `product.template`. The `self_order_available` and `self_order_visible` fields and their logic simply moved out of `product_product.py` into a dedicated `models/product_template.py` file.
- `ir.ui.view` is now part of the models loaded for self-ordering (`_load_pos_self_data_read` loads QWeb templates through `_get_xml_ids_to_load()`).
- `pos.config` no longer sends `_pos_special_products_ids`. Each product template now carries a per-record boolean `_is_pos_special_product`.
- `mail.template` now declares `_load_pos_data_dependencies() -> ['pos.preset']`.

**Loading architecture (the main change)**

- `_load_pos_self_data_search_read()` is removed. It is replaced by a two-step pipeline: `_load_pos_self_metadata()` (search) then `_read_pos_self_data_from_metadata()` (read), both driven by `_load_pos_self_data_domain_and_relations()`.
- `load_self_data()` now returns that pipeline's result; `load_data_params()` disappears from `pos.config` in this addon.
- Breaking signature change: `_load_pos_self_data_domain(self, data, config)` becomes `_load_pos_self_data_domain(self, data)`. The configuration is read from `data['pos.config']`. Same for `_load_pos_data_domain`.
- Domains now use record sets taken from already-loaded data (e.g. `data['pos.preset'].mail_template_id.ids`, `data['restaurant.floor'].ids`) instead of re-searching.
- `write_date` is automatically appended to every loaded field list, so the frontend can request only records newer than its cache.
- Models that raise an `AccessError` are no longer fatal: they are logged and loaded as an empty list.
- `product.template._load_pos_self_data_read()` no longer searches products itself; it sorts and reads the records prepared by metadata, and the special-product flag is added there.
- `product.product._send_availability_status()` now requires `config.current_session_id` before pushing availability to self-order screens.

## How your habits should change

- **Faster start-up on kiosks and mobiles.** Self-order screens now receive their data incrementally: the browser keeps its IndexedDB cache and only downloads records changed since the last load. On busy sites this means less waiting at boot and lower server load.
- **Stale caches clean themselves up.** If a module is uninstalled, the corresponding data is removed from the browser cache instead of lingering.
- **Product availability is only pushed when a session is open.** Changing "Available in Self Order" while no POS session is running no longer notifies the terminals; open a session to propagate the change.
- **Customisations must be updated.** Any in-house module overriding the self-order loading methods must drop the `config` argument and read the configuration from `data['pos.config']`, and must use the new metadata/read hooks instead of `_load_pos_self_data_search_read`. Plan this work in the migration budget.

## What you gain by migrating

- A more scalable, incremental data loading mechanism, designed for large catalogues and multiple terminals.
