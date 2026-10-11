# pos_loyalty migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 release notes contain **no Point of Sale loyalty section**. The loyalty entries listed there (loyalty rules for subscriptions, loyalty point expiration) belong to the Sales / Subscriptions side and are not documented as POS features. Everything below therefore comes from the module refactor itself (odoo/odoo#274951).

- Free product rewards are **no longer added automatically** to the cart. You claim them from the Rewards button, and they show up as **zero-priced** order lines instead of negative-price lines.
- The **Reset Rewards** button is gone. You remove a reward by deleting its line with the numpad (this disables it), and re-add it any time through Rewards.
- You can spend loyalty points even if the order contains nothing else: the old "a reward can only be claimed if something is being paid for" rule no longer applies.
- A reward's product is **locked** once claimed; only its quantity can be changed. The same reward can no longer be applied twice on one order, matching Sales loyalty.
- Refunding an order **gives the points back**, as in Sales loyalty.
- Gift cards / eWallets: the customer popup opens as soon as an eWallet top-up is added without a partner; the eWallet pay button is simply disabled when no valid eWallet can be added; the gift card program is asked only once.
- Balances stay correct: after validation, touched cards are pushed back to the POS, so a gift card sold and redeemed in the same session now shows the right balance.

## Technical data model changes

- `loyalty.card`: `source_pos_order_partner_id` removed; `source_pos_order_id` is now readonly with a `btree_not_null` index; `use_count` counts `pos.order.line.card_id` (was `coupon_id`); `get_gift_card_status` replaced by `get_card_status(code, config_id)`; `get_loyalty_card_partner_by_code`, `_get_default_template`, `_get_signature` and the `_mail_get_partner_fields` override removed; new `_get_or_create_pos_card` and `create_pos_cards`.
- `pos.order.line`: `coupon_id` renamed to `card_id`.
- `pos.order`: new `applied_codes` (Json); `reward_identifier_code` removed; `confirm_coupon_programs` / `validate_coupon_programs` replaced by `_process_loyalty`, called from `_process_saved_order` for validated orders; new `read_pos_data` returns the cards to refresh.
- `res.partner`: `loyalty_card_count` removed.
- `loyalty.reward`: the `_get_discount_product_values` override is gone (discount lines keep their taxes); new `_get_pos_points_cost`.
- `loyalty.rule` / `loyalty.program`: new `_get_pos_order_points`, `_is_fulfilled`, `_qualifying_lines`, `_in_domain`, `_counts_for_points`; `promo_barcode` and `is_payment_program` are now loaded to the POS.
- `pos.config`: `_get_program_ids(check_usage=True)` new parameter; `use_coupon_code` and the `_check_before_creating_new_session` gift-card/reward validations removed.

## How your habits should change

- Brief cashiers: free products are claimed manually and shown at 0, not as a negative discount line.
- Stop looking for Reset Rewards; deleting a reward line is now how you disable it.
- Expect a customer popup when adding eWallet top-ups or gift cards: select the customer first.
- Tell customers points come back on refunds, but a given reward can only be used once per order.

## What you gain by migrating

- Loyalty points, discounts and card histories are recomputed server-side on validation, so results can be trusted even if the POS ran offline — only gift card / eWallet sales need a connection.
- Behaviour is now aligned with Sales and eCommerce loyalty, which makes setup and training simpler.
- Fewer cashier mistakes and clearer receipts (0-price reward lines instead of negative lines).
- Staying on 19.0 keeps the old negative-line rewards, the Reset Rewards button, and no server-side recomputation.
