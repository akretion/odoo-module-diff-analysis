# pos_self_order migration guide (19.0 -> 20.0)

The 20.0 release notes contain no section dedicated to `pos_self_order`; the published highlights cover other (largely Enterprise) areas and are not reproduced here. This guide is therefore based on the actual code delta of the addon between 19.0 and 20.0.

## What's new for users
No official release note describes a functional change in Self Order for 20.0. The impact of this version is mostly "under the hood", yet it reaches the screen:
- Self-order and kiosk devices now sync incrementally: only records changed since the last load are fetched again, so opening a device or refreshing the catalogue is lighter and quicker.
- Cached data in the browser is cleaned automatically, so stale screens/configurations are less likely.
- Self-order screens (QWeb templates) are shipped to the device as data, reducing "missing screen" glitches after an upgrade.
- Products flagged as special (tip, service fee, delivery) are now marked individually during product loading instead of being deduced from a config list.
- Order validation, combos, line prices, subtotals, service fees and receipts gained dedicated server-side checks, making totals more reliable.
- Presets gain delivery-address helpers (composed address and geolocation) for delivery orders.
As these points are code-level, validate them on a staging copy before advertising them to shop staff.

## Technical data model changes
- **Model split**: `product.template` logic moved out of `models/product_product.py` into a new `models/product_template.py`. Fields `self_order_available` / `self_order_visible` are unchanged, but modules inheriting the old file path must be updated.
- **New `ir.ui.view` extension** with `_get_xml_ids_to_load()` / `_load_pos_self_data_read()`; `ir.ui.view` is now part of the self-order loaded models.
- **Loading API**: `_load_pos_self_data_domain(self, data)` — the `config` argument is gone, the configuration is read from `data['pos.config']`. Same for `_load_pos_data_domain(self, data)`.
- **Removed**: `_load_pos_self_data_search_read()`, `pos.config.load_data_params()`, `_get_self_ordering_attachment`, `_onchange_payment_method_ids`, `_split_qr_codes_list`, `_supported_kiosk_payment_terminal`, `res.config.settings.update_access_tokens` / `_onchange_pos_payment_method_ids`, `product.product._filter_applicable_attributes`.
- **Added in `pos.load.mixin`**: `_load_pos_self_data_domain_and_relations()`, `_load_pos_self_metadata()`, `_read_pos_self_data_from_metadata()`; `mail.template._load_pos_data_dependencies()`. `_load_pos_self_data_read(records, config)` now takes a recordset, and `write_date` is appended to loaded fields for cache comparison.
- **New/changed per model**: `pos.config` → `_load_self_metadata`, `_can_use_cash_payment_method`, `get_dynamic_qr_url(order_id)`, `close_ui`, `notify_session_state_changed`, `_get_self_order_route(table_id=None, order=None)`; `pos.order` → combo/price/preparation/receipt checks, `recompute_prices`, `cancel_order_from_pos`; `pos.order.line._handle_combo_parent_uuid`; `pos.preset` → `create/write/default_get` and delivery-address methods; `pos.session.close_session_from_ui(payment_method_closing={})`, `_set_opening_control_data`; `pos.snooze._sync_snoozes`; `res.config.settings.get_pos_qr_stands`, `pos_close_ui`.

## How your habits should change
- No documented 20.0 release note changes the day-to-day ordering flow: expect the same journey, with faster data refresh.
- Technically, stop passing `config` to the self-order loading hooks: it now travels inside `data`. Replace any use of `_load_pos_self_data_search_read` with the metadata + read pair.
- Custom overrides of `load_data_params`, `_load_pos_data_domain` or `_load_pos_self_data_read` must be adapted before upgrading.
- If you customized `product.template` through the old file, move that code next to the new `product_template.py`.

## What you gain by migrating
- Incremental, `write_date`-based synchronisation instead of full reloads: faster kiosk start-up and less bandwidth.
- A single `load_data` entry point with metadata separated from records, making custom modules simpler and more durable.
- Automatic cleanup of stale browser caches when models are uninstalled or changed.
- Stronger order validation: combos, line prices, subtotals, service fees and receipts are checked server-side.
- A supported path: staying on 19.0 means maintaining custom loading code that no longer matches the 20.0 API.
