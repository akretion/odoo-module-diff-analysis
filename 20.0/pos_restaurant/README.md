# pos_restaurant migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 release notes excerpt available to us only covers **industry packages** (Hotel/Hospitality, Catering, Vineyard, Property Developer, etc.). None of them documents `pos_restaurant`, and those packages are Enterprise-oriented, so nothing from that excerpt is presented here as a Community feature. The changes below come from the module's own 19.0 → 20.0 history.

**1. A rebuilt Floor Plan editor.** Floors are no longer just a coloured background with tables on top. You can now place and edit **decorative elements directly on the floor plan: shapes, text items and images**, freely positioned and resized. Images are uploaded from the editor (PNG, JPEG, JPG, GIF, SVG, WEBP are accepted) and reused if the same file is uploaded twice.

**2. Tips: fixed amount or percentage.** Tip handling was made more flexible: you choose between a **fixed amount** and a **percentage of the order total**. The "Set Tip After Payment" option was **moved out of `pos_restaurant` into the core POS module**, so it is no longer a restaurant-only setting — it is available on every point of sale configuration.

## Technical data model changes

**`restaurant.floor`**
- Removed: `background_image`, `background_color`, `floor_background_image`.
- Added: `floor_plan_layout` (Json) holding the whole layout, including background and decorations.
- `_load_pos_data_fields()` returns only `name`, `table_ids`, `sequence`, `pos_config_ids`.
- Removed method: `sync_from_ui(self, name, background_color, config_id)`.
- Added: `add_floor_plan_image(self, name, data)` (validates MIME type, deduplicates by checksum, returns attachment id/url) and an autovacuum `_gc_embeddings()` that deletes floor-plan images not linked to a floor after one day.

**`restaurant.table`**
- Removed: `shape`, `position_h`, `position_v`, `width`, `height`, `color`.
- Added: `parent_side` (Selection: left/right/top/bottom) and `floor_plan_layout` (Json).
- `_load_pos_data_fields()` now returns `table_number`, `parent_id`, `parent_side`, `floor_id`, `seats`, `active`.

**`pos.config`**
- Added: `floor_plan_settings` (Json, stored) and `floor_plan` (Json, computed from active floors/tables).
- Removed: `set_tip_after_payment` (now defined in `point_of_sale`).
- New methods: `_compute_floor_plan()`, `get_floor_plan()`, `save_floor_plan(self, floor_plan)`, `_save_floor_plan_floor()`, `_save_floor_plan_table()`, `_split_layout_server_data(self, data, model_keys, layout_exclude_keys)`. `save_floor_plan()` creates/updates/deactivates floors and tables, refuses to silently drop tables that still have draft orders, and broadcasts a `FLOOR_PLAN_UPD` notification to other POS devices.

**`res.config.settings`**
- Removed: `pos_set_tip_after_payment` and its compute method.

Standard upgrade scripts (odoo/upgrade) carry your existing floor plans, colours and table geometry into the new JSON layout fields.

## How your habits should change

- **Configure floors in the POS floor plan editor, not in the back office.** Background colour, background image and per-table colour/shape/position/size are no longer separate fields: they live inside the floor plan layout.
- **Use the editor's decoration tools** (shapes, text, images) to reproduce what you used to build with background images; floor plans can now be designed from scratch in Odoo.
- **Upload images through the editor.** Unsupported formats are rejected with a clear error message; avoid re-uploading the same picture repeatedly, but duplicates are deduplicated automatically.
- **Look for "Set Tip After Payment" in the general POS settings**, not in the restaurant section, and pick fixed vs percentage when defining tips.
- Deleting a floor or table in the editor deactivates it; a table with a draft order is protected.

## What you gain by migrating

- Richer, self-designed floor plans with decorations, free positioning and easier maintenance.
- One consistent tip mechanism for all POS configurations, with fixed or percentage amounts.
- Cleaner data model: geometry and styling stored as structured layout data instead of a scatter of pixel fields.
- Safer floor-plan editing: draft orders protected, removed tables deactivated rather than lost, and other terminals notified in real time.
- Automatic cleanup of unused floor-plan images keeps the database tidy.

If you run a heavily customised floor plan, budget a short functional review of your floors and tables with your integrator before and after the upgrade.
