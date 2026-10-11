# pos_loyalty migration guide (17.0 -> 18.0)

## What's new for users

**Sell physical gift cards from the POS terminal.** Gift card codes no longer have to be generated automatically. You can now sell a physical gift card and type the card number by hand; the POS order then creates a gift card carrying exactly that number. Existing gift cards can also be topped up (amount added) directly from the terminal.

**Gift card setup is simpler.** The per-POS "Gift Cards settings" choice (Generate PDF cards / Scan existing cards) has been removed. The POS now works with both flows at once, and an email template plus a print report are always required on a gift card program: opening a session is blocked with a clear message if they are missing.

**About the official release notes:** the 18.0 Sales notes contain no section describing pos_loyalty itself. The closest item, "Portal: loyalty cards and eWallet", belongs to the Loyalty app's customer portal (balance consultation), not to the POS terminal. The user-visible changes for this addon therefore come from the code, as described here.

## Technical data model changes

- `loyalty.card`, `loyalty.program`, `loyalty.reward` and `loyalty.rule` are no longer plain `_inherit` extensions: each now declares `_name` plus `_inherit = [..., 'pos.load.mixin']`, meaning they are loaded into the POS through the standard POS data-loading mixin.
- **Removed:** `pos.config.gift_card_settings` (Selection) and `res.config.settings.pos_gift_card_settings` (related field). The whole `models/res_config_settings.py` file is deleted.
- `pos.session`: the `_load_data_params()` and `load_data()` overrides were removed. Loading is now declared through `_load_pos_data_models()` (returning `loyalty.program`, `loyalty.rule`, `loyalty.reward`, `loyalty.card`) and, per model, `_load_pos_data_domain()` / `_load_pos_data_fields()`.
- New `product_product.py`: `_load_pos_data_fields()` adds `all_product_tag_ids`; `_load_pos_data()` appends loyalty reward and trigger products (from `discount_line_product_id`, `reward_product_ids`, `reward_product_id`). `_pos_special_products_ids` is now carried on the `pos.session` payload instead of the old `custom` payload.
- `pos.order.line._load_pos_data_fields()` extends the base field list with `is_reward_line`, `reward_id`, `reward_identifier_code`, `points_cost`, `coupon_id`.
- `loyalty.reward._load_pos_data()` now converts `reward_product_domain` `ilike` / `not ilike` conditions on many2one fields into `in` / `not in` with resolved ids, server side (previously done in `pos.session`).
- `pos.order`: the coupon code used when creating cards is taken from `code` first, falling back to `barcode`, then to a generated code.

## How your habits should change

- Forget the POS gift-card mode selector: there is nothing left to configure on the POS configuration for gift cards.
- Before using gift cards on a POS, always fill in the communication email template and the ticket print report on the gift-card loyalty program, otherwise the session cannot be opened.
- Cashiers can sell a physical gift card like any other product and enter its number; the same action also lets them reload an existing card.

## What you gain by migrating

- Real physical gift-card sales and reloads at the counter, with manually entered card numbers.
- One consistent gift-card flow per program, with fewer per-POS settings to keep aligned.
- A standard, mixin-based data-loading path (`pos.load.mixin`) instead of hand-written session overrides: easier to maintain and extend, and far less likely to break when the POS payload evolves.
