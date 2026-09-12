# pos_restaurant migration guide (19.0 -> 20.0)

This guide covers what changes for the `pos_restaurant` addon between Odoo 19.0 and Odoo 20.0 (Community edition).

## What's new for users

- **A brand-new floor plan editor.** You can now add decorative elements to a floor plan: shapes, text items and images (including a background image), placed and edited directly on the plan. Customising a restaurant layout is no longer limited to coloured square/round tables.
- **More flexible tips and discounts.** You can choose between a fixed amount and a percentage of the order total, instead of a single behaviour.
- **"Set Tip After Payment" is no longer a restaurant-only setting.** It moved from `pos_restaurant` into the core `point_of_sale` module, so it can be configured on any POS configuration, not only restaurants. Restaurant-related modules that relied on this setting or on the tip logic were adjusted accordingly.

Note: no official release-note extract matched this addon, so the points above are derived from the code changes shipped in 20.0.

## Technical data model changes

`pos.config`
- Added `floor_plan_settings` (Json) and `floor_plan` (computed Json).
- Removed `set_tip_after_payment` (moved to `point_of_sale`).
- New methods: `_compute_floor_plan`, `get_floor_plan`, `save_floor_plan`, `_save_floor_plan_floor`, `_save_floor_plan_table`, `_split_layout_server_data`. `_compute_local_data_integrity` now depends on `floor_plan_settings`.

`res.config.settings`
- Removed `pos_set_tip_after_payment` and its compute method.

`restaurant.floor`
- Removed `background_image`, `background_color`, `floor_background_image`, `sync_from_ui` and `rename_floor`.
- Added `floor_plan_layout` (Json), `add_floor_plan_image(name, data)` (validates GIF/JPEG/JPG/PNG/SVG/WebP uploads) and a `_gc_embeddings` autovacuum that deletes unused floor-plan images after one day.
- `_load_pos_data_domain(self, data)` no longer receives `config`.

`restaurant.table`
- Removed `shape`, `position_h`, `position_v`, `width`, `height` and `color`.
- Added `parent_side` (selection), `floor_plan_layout` (Json) and `set_parent_id(...)`.
- `_load_pos_data_domain(self, data)` no longer receives `config`.

Other signature changes
- New: `_load_pos_data_fields` on `pos.category` and `pos.course`; `create` and `_load_pos_data_dependencies` on `restaurant.order.course`; `_compute_duration` on `pos.order`; `_default_sequence` on `pos.course`; `default_get` on `product.template`.
- Removed: `pos.payment._update_payment_line_for_tip` and `pos.session._set_last_order_preparation_change`.

Migration scripts are shipped with the upgrade path (floor plan layout and tip setting), so existing floors, tables and settings are converted rather than lost.

## How your habits should change

- Design floors in the new floor plan editor. Table size, colour and position are no longer stored on the table record; they live in the floor plan layout JSON and are edited visually.
- A floor's background is now an image set inside the floor plan, not a `background_color` or background image field on the floor.
- Creating and renaming floors goes through the floor plan editor; the old "sync from UI" and rename calls are gone.
- "Set Tip After Payment" is configured in the general POS settings of any POS configuration; the restaurant-specific toggle disappeared from the restaurant settings screen.
- When taking tips or discounts, pick the fixed or percentage mode that suits the order.
- The same image reused on two floors is duplicated per floor, so image management stays predictable.

## What you gain by migrating

- A much richer floor plan designer: shapes, text and images to match your real room layout.
- Flexible tips and discounts (fixed amount or percentage).
- Tip-after-payment available to all POS configurations, with a simpler settings screen.
- A cleaner, more consistent data model (JSON layouts, fewer legacy fields) aligned with 20.0 and future versions.
- Your existing floors, tables and configuration are carried over by the upgrade scripts.
