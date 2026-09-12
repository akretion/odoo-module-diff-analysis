# pos_loyalty migration guide (15.0 -> 16.0)

## What's new for users

In 16.0 the Point of Sale joins the new multi-channel loyalty model:

- **Coupons, loyalty cards, promotions and gift cards are shared** between the Point of Sale, Sales and eCommerce. Programs are defined once and their usage is tracked across every channel.
- **PoS is aligned with the same loyalty programs** used by the Sales and Website apps instead of keeping its own PoS-only selection.
- **PoS settings were reorganised** - PoS configurations are now managed from the General Settings screen, and the old loyalty blocks are gone from that page.

Other items in the official Point of Sale release notes (eWallets, global barcode nomenclature, margins and costs visibility, order auto-confirmation, invoice/portal requests, simplified interface) belong to other addons and are **not** provided by `pos_loyalty`.

## Technical data model changes

`loyalty.program`
- `pos_config_ids` used to be a read-only "Point of Sales" Many2many. It is now computed, stored and editable (`compute="_compute_pos_config_ids"`, depends on `pos_ok`), and is cleared when `pos_ok` is unset. An empty list means the program applies to **all** PoS.

`pos.config`
- Removed fields: `use_coupon_programs`, `coupon_program_ids`, `promo_program_ids`, `loyalty_program_id`, `use_gift_card`, `gift_card_program_id` and the computed `all_program_ids`.
- Removed helpers: `_default_loyalty_program()` and `set_loyalty_program_to_main_config()`.
- New helper `_get_program_ids()` replaces `all_program_ids` everywhere (session loading, coupon code search, product loading, validation before opening a session). It returns programs with `pos_ok = True` that either list this config or list none.
- `gift_card_settings`: the `scan_set` option disappears; the remaining choices are renamed "Generate PDF cards" and "Scan existing cards".
- `_check_before_creating_new_session()` now loops over all gift card programs of the config (from `_get_program_ids()`) instead of a single `gift_card_program_id`.

`pos.session`
- Loaders use `_get_program_ids()`; loyalty points are loaded from the first loyalty-type program.
- New `_pos_data_process()` keeps the front-end keys `loyalty_program_id`, `coupon_program_ids`, `promo_program_ids`, `use_coupon_programs`, `gift_card_program_id` and `use_gift_card` filled from the loaded programs. The code flags them as temporary, so they are internal compatibility shims, not configuration.

`res.config.settings`
- Removed: `pos_loyalty_program_id`, `pos_use_coupon_programs`, `pos_coupon_program_ids`, `pos_promo_program_ids`, `pos_use_gift_card`, `pos_gift_card_program_id` and `_compute_pos_loyalty_id`. Only `pos_gift_card_settings` remains, related to `pos.config`.

No method signature changes for other addons. An upgrade script (odoo/upgrade#3842) accompanies this change.

## How your habits should change

- Create and maintain loyalty, coupon, promotion and gift card programs **in the Loyalty app**, once. Multi-channel programs replace per-PoS program selection.
- Forget the "Coupons & Promotions" and "Gift Cards" checkboxes and the "PoS Loyalty Program" field on the PoS configuration. Assign programs to points of sale from the program form.
- Leave the "Point of Sales" field empty on a program to apply it to every PoS; fill it to restrict the program to specific counters.
- In PoS settings (General Settings), only the gift card setting is left.
- Gift card setup is simpler: choose between generating PDF cards and scanning existing cards.
- Before opening a session, make sure reward products of every program, including gift card programs, are available in PoS.

## What you gain by migrating

- A single loyalty engine for the counter, the shop and the website: same cards, same points, same coupons, tracked everywhere.
- Less duplicated configuration, fewer mistakes, and no risk of a program being active in Sales but forgotten at the PoS.
- Programs become automatically applicable when no PoS restriction is set.
- Clearer gift card handling with simplified settings and stronger validation when a session starts.
- A maintained, forward-compatible codebase: the old PoS-only fields are gone instead of being kept as dead weight.
