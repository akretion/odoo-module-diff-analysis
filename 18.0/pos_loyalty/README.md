# pos_loyalty migration guide (17.0 -> 18.0)

## What's new for users

The 18.0 release notes extract is mostly Sales-oriented and says little about Point of Sale loyalty. Only one entry concerns this addon:

- **Portal: loyalty cards and eWallet** — customers with portal access can now consult the balance of their loyalty cards and eWallet (the addon contributes the POS-side loyalty history, `add_loyalty_history_lines`).

The addon's own changes also bring a notable gift card evolution:

- The POS-level "Gift Cards settings" choice (Generate PDF cards / Scan existing cards) is gone; gift card behaviour is now decided by the gift card program/product configuration.
- Cashiers can **sell a physical gift card and type its number manually**; the entered number is reused as the card code, and an amount can be added to an existing card.

Fixes worth noting: discount rewards on a specific product category now apply correctly; the same loyalty card is no longer created twice for a customer; rewards can now be deleted.

## Technical data model changes

- `loyalty.card`, `loyalty.program`, `loyalty.reward` and `loyalty.rule` now inherit `pos.load.mixin` and each declares `_load_pos_data_domain` / `_load_pos_data_fields`.
- `pos.session`: `_load_data_params`, `load_data`, `_get_pos_ui_loyalty_*`, `_get_pos_ui_product_product`, `_get_pos_ui_res_partner`, `_loader_params_*`, `_pos_data_process`, `_pos_ui_models_to_load`, `_set_loyalty_cards` and `get_pos_ui_res_partner_by_params` were **removed**; `_load_pos_data_models` was added.
- `loyalty.reward`: new `_load_pos_data`, `_get_reward_product_domain_fields`, `_parse_domain`, `_replace_ilike_with_in` (moved here from `pos.session`) and `unlink()`.
- `product.product`: new `models/product_product.py` with `_load_pos_data` and `_load_pos_data_fields` (adds `all_product_tag_ids`). Reward products and eWallet trigger products are now loaded through the standard POS data mechanism instead of a custom `load_data` block.
- `pos.order.line`: `_export_for_ui` and `_order_line_fields` removed; `_load_pos_data_fields` now adds `is_reward_line`, `reward_id`, `reward_identifier_code`, `points_cost`, `coupon_id`.
- `pos.order`: `_add_mail_attachment` signature becomes `(name, ticket, basic_receipt)`; added `_check_existing_loyalty_cards`, `_get_fields_for_order_line` and `add_loyalty_history_lines`.
- Removed fields/model: `pos.config.gift_card_settings`, `res.config.settings.pos_gift_card_settings` (file `res_config_settings.py` deleted).
- POS data loading now accepts arbitrary server-side values through `_`-prefixed keys.

## How your habits should change

- Gift cards: no longer a POS configuration setting — decide it when setting up the gift card program/product. Use manual code entry to sell pre-printed cards, and top up an existing card instead of creating a new one.
- Customers: tell them they can follow their loyalty balance from the portal.
- Customizations: any custom override built on `_loader_params_*`, `_get_pos_ui_*`, `_pos_data_process` or `load_data` must be rewritten for the new `pos.load.mixin` / `_load_pos_data*` API.

## What you gain by migrating

- A single, consistent POS data-loading API, with much less duplicated code.
- Loyalty programs, rules, rewards and cards are loaded per POS configuration, including product tags and reward product domains.
- Fewer duplicate loyalty cards, working reward deletion, correct category discounts.
- Physical gift cards with manual numbers and top-ups match real retail flows.
- Customer-facing portal balances for loyalty cards and eWallet, reducing shop queries.
- 18.0 is the maintained release: staying on 17.0 means missing these fixes and future security and bug updates.
