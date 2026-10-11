# pos_self_order migration guide (18.0 -> 19.0)

## What's new for users

- **Presets in self-ordering.** The "Self Takeaway" checkbox is gone. Self-order now uses Point of Sale *presets*: each preset states whether it is available in self-order and how the order is served (Pickup zone, Table or Delivery). This matches the 19.0 "PoS presets" release note — customers pick a schedule/mode instead of a binary takeaway flag.
- **Products come from product templates.** The self-order catalog is built from `product.template` instead of `product.product`. Variants, attributes and pricelists are handled natively, removing a long-standing performance/limit issue on product cards.
- **More reliable loading and receipts.** Self-order and the POS terminal now share one data-loading pipeline; session data (base URL, self-order marker) is attached consistently, fixing cases where receipts failed to print when the session object was missing in self-order.

Note: self-order items in the release notes about restaurants (allergens, courses, preparation-time report) are Enterprise features, not part of this Community addon.

## Technical data model changes

Removed: `account.fiscal.position` no longer inherits the self-order loading mixin in this addon (its file is deleted).

Removed fields:
- `pos.config.self_ordering_takeaway` ("Self Takeaway")
- `res.config.settings.pos_self_ordering_takeaway` (related)

Added fields on `pos.preset`: `available_in_self` (Boolean, default False) and `service_at` (Selection: counter / table / delivery, default counter, required).

Loading API reworked on all models:
- `_load_pos_self_data_domain(self, data)` → `_load_pos_self_data_domain(self, data, config)`
- `_load_pos_self_data_fields(self, config_id)` → `_load_pos_self_data_fields(self, config)`
- `_load_pos_self_data()` and `_post_read_pos_self_data()` are replaced by `_load_pos_self_data_read(self, records, config)` and the new `_load_pos_self_data_search_read(self, data, config)`.
- `config` is now a `pos.config` record, not an id or `data['pos.config'][0]`; domains read the config directly (e.g. `config.floor_ids.ids`, `config.default_preset_id.id`).

Other models:
- `pos.session`: `_post_read_pos_data` / `_post_read_pos_self_data` replaced by `_load_pos_data_read`; `_base_url` and `_self_order_pos` are no longer set on the session payload.
- `pos.order`: the `sync_from_ui` override (takeaway preservation) is removed.
- `product.product`: loading fields, onchange and write hooks move to `product.template`; the product card model is `product.template`.
- `res.partner`: self-order domain returns `False` (no partner data loaded).
- `mail.template`, `product.tag`, `pos.payment.method`, `restaurant.floor`, `restaurant.table`, `pos_self_order.custom_link` were updated to the new signatures.

## How your habits should change

- Stop configuring the "Self Takeaway" option in POS settings: it no longer exists. Create presets instead, enable "Available in Self" on the ones to expose, and set "Service at" (Pickup zone, Table, Delivery).
- Review every preset (schedule, mail template, service location): order mode is now chosen per preset, not globally.
- Product availability in self-order still uses `self_order_available` / `public_description`, now stored and loaded on `product.template`; unchecking "Available in POS" still clears self-order availability.
- Custom developments must adopt the new `(data, config)` signatures and `_load_pos_self_data_read`; the old `_post_read_pos_self_data` hook no longer exists.

## What you gain by migrating

- Real service-mode choices for customers (pickup, table, delivery) instead of one takeaway flag.
- Attributes, variants and pricelists usable in the self-order catalog, with better performance on large catalogs.
- One loading pipeline shared with the POS terminal: fewer edge cases, reliable session/base-URL data and printing.
- Less dead code (fiscal-position shim, takeaway overrides) and simpler customizations.
