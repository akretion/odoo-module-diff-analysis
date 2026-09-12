# pos_restaurant migration guide (16.0 -> 17.0)

This guide summarizes what changed in the `pos_restaurant` addon between Odoo 16.0 and 17.0, for functional users preparing an upgrade.

## What's new for users

- **Cross-session floor plans and orders.** A floor plan is no longer tied to a single cashier desk: it can be shared by several Point of Sale configurations. Ongoing (draft) orders placed on a shared floor are visible from every desk of the same database, and past orders can be consulted from any desk. This is the "cross-orders" feature announced in the Odoo 17 release notes.
- **Floor map enabled by default.** For restaurant/bar PoS configurations, the floors and tables map no longer needs to be activated manually.
- **Preparation tools available everywhere.** The preparation display and the preparation (kitchen) printer logic moved from `pos_restaurant` into the core Point of Sale module, so they are no longer exclusive to restaurant setups.
- **Clearer wording.** "Kitchen Notes" is renamed "Internal Notes" (on the configuration and on order lines).

Note: other PoS items of the 17.0 release notes (self-order QR codes, self-service kiosk, table booking, online payment) are not part of this Community addon and are not covered here.

## Technical data model changes

**Models**
- `restaurant.printer` is removed; kitchen printing logic now lives in `point_of_sale`.
- Several order loading/export helpers (`_get_order_lines`, `_get_payment_lines`, `_prepare_order_line`, `remove_from_ui`, ...) were moved to `point_of_sale` or replaced by core hooks.

**Fields**
- `pos.config.floor_ids`: `One2many` -> `Many2many`.
- `restaurant.floor.pos_config_id` (`Many2one`) becomes `pos_config_ids` (`Many2many`, restricted to restaurant-enabled configs).
- `res.config.settings.pos_floor_ids`: `One2many` related -> `Many2many` related; helper `_get_floors_domain()` removed.
- Removed from `pos.config`: `printer_ids`, `is_order_printer`.
- Removed from `pos.order.line`: `uuid`, `mp_skip`; `note` is relabeled but kept.
- Removed from `pos.order`: `multiprint_resume`.
- Removed from settings: `pos_is_order_printer`, `pos_printer_ids`.
- `iface_orderline_notes` is relabeled "Internal Notes".

**Behavior**
- The one-floor/one-desk limitation disappears (the "already used in another Pos Config" error is gone). A floor linked to a configuration with an open session still cannot be modified or archived.
- Session loading now filters floors by `pos_config_ids` instead of `pos_config_id`.
- `pos_restaurant` now only extends the draft-order field lists and adds a `_prepare_order` hook on top of the core implementation.

## How your habits should change

- **Multi-desk setups:** stop duplicating a floor plan per cashier desk. Create one floor and assign it to all relevant PoS configurations; tables and running orders are then shared.
- **Printers:** kitchen/preparation printers are configured through the general Point of Sale printer settings, not the restaurant-specific section. The old "Order Printer" toggle is gone, so printers must be re-checked after migration.
- **Nothing to do** for the floor map setting: it is active by default.
- The notes rename is purely cosmetic; existing notes are preserved.

## What you gain by migrating

- Multi-desk restaurants work out of the box: shared floors, shared running orders, shared order history.
- Much less duplicated configuration when several cashier desks operate the same room.
- Preparation display and preparation printers usable on any Point of Sale, not only restaurants.
- A data model aligned with the core Point of Sale, which makes future upgrades and custom developments simpler.
