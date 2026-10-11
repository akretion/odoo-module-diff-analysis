# pos_self_order migration guide (17.0 -> 18.0)

## What's new for users

The 18.0 release notes have no dedicated "Self Order" entry. The Point of Sale improvements they list also reach this addon in Community: the redesigned POS front end, creating/editing products directly from the POS, payment methods still editable before the receipt is printed, cash counts always logged on the session, and the customer display usable on any device without an IoT box. Restaurant internal notes and table merging come from pos_restaurant (Community).

The user-visible change specific to this addon is takeaway. It is no longer a self-order-only option: you enable it once on the POS configuration and a Takeaway toggle appears in the main POS interface, not only on the mobile/kiosk self-order screens. A single takeaway fiscal position now drives takeaway prices and taxes for both channels. The QR-code payment and AvaTax items in the release notes are Enterprise/paid-app features and are not part of this Community module.

## Technical data model changes

**pos.config**
- Removed `self_ordering_alternative_fp_id` (Many2one to account.fiscal.position); takeaway tax mapping now uses the core `takeaway_fp_id`.
- Removed the local `access_token` field, the `_get_access_token()` static method and the `_init_access_token()` model method. `pos.config` now inherits `access_token` and `_notify()` from the new `pos.bus.mixin`. `_update_access_token()` remains, simplified to assigning a new uuid.
- `self_ordering_takeaway` is kept; its label changed from "Takeaway" to "Self Takeaway".
- `action_close_kiosk_session()` no longer pushes to `bus.bus` directly; it calls `_notify('STATUS', ...)`.
- `_get_self_ordering_payment_methods_data()` now reads fields from `pos.payment._load_pos_data_fields()` instead of `current_session_id._load_data_params()`.

**pos.order**
- Removed the `take_away` Boolean field; the core `takeaway` field is used instead.
- Removed the `create_from_ui(orders, draft=False)` override.
- `_order_fields()` and `_export_for_self_order()` now use the payload key `takeaway` instead of `take_away`.

**pos.order.line**
- Removed `combo_parent_id` (Many2one) and `combo_line_ids` (One2many); combo lines are linked through `combo_id`.

**pos.session**
- Removed the `_load_data_params(config_id)` and `load_data(models_to_load, only_data)` overrides. Replaced by `_load_pos_data(data)`; the self-ordering availability flag now travels as custom data under the `_self_ordering` key.

**product.product**
- New `_load_pos_data_fields(config_id)`, adding `self_order_available`.
- Price and tax computation uses `pos_config.takeaway_fp_id` instead of `self_ordering_alternative_fp_id`.

**res.config.settings**
- Removed the related field `pos_self_ordering_alternative_fp_id`.

## How your habits should change

- Configure takeaway, and its fiscal position, once in the POS configuration; there is no separate self-order fiscal position anymore. Clean up any saved filters, templates or settings pages referencing it.
- Reports, exports or integrations reading `take_away` on orders or order payloads must be switched to `takeaway`; the old key is gone.
- The self-order takeaway switch still exists, now labelled "Self Takeaway": it controls whether self-order customers see the takeaway option.
- Custom modules overriding `create_from_ui`, `load_data`, `_load_data_params`, `_get_access_token` or `_init_access_token` must be ported: use `_notify()` from `pos.bus.mixin`, `_load_pos_data()` / `_load_pos_data_fields()`, and let the mixin manage tokens.
- Combo customizations relying on parent/child order lines should use `combo_id`.
- Studio or custom fields pointing at removed fields will break on upgrade; re-map them before migrating.

## What you gain by migrating

- Takeaway available in the main POS *and* in self-order, with one takeaway fiscal position for consistent taxes and prices, instead of two parallel settings.
- Real-time notifications rebuilt on `pos.bus.mixin` with per-record tokens and `bus.subscribe` on the front end: more reliable order and product updates in kiosk and QR-menu sessions.
- A standard way to send server-computed data to the POS using `_`-prefixed keys, so custom information no longer forces model changes.
- Simpler combo handling on order lines.
- Less custom glue code overall: the addon now follows Community core APIs, easing future upgrades.
