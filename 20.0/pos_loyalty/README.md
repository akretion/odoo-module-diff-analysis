# pos_loyalty migration guide (19.0 -> 20.0)

The official 20.0 release notes contain no section matching this addon (they cover other, mostly Enterprise, apps). Everything below therefore comes from the code itself, driven by one large refactor: *"[REF] pos_loyalty, pos_*_loyalty: complete refactor of loyalty for pos"* (PR 274951), plus smaller fixes. All of it applies to the Community edition.

## What's new for users

- **Free product rewards are claimed manually.** They are no longer added to the cart automatically as a negative-price line. They now appear as a 0.00 priced order line, claimed from the Rewards buttons.
- **The "Reset Rewards" button is gone.** Remove a reward line with the numpad to disable that reward; add it back at any time through the normal Rewards button.
- **Rewards can be used on their own.** A customer may spend loyalty points without having to buy anything else on the same order.
- **No duplicate rewards.** The same discount reward can no longer be applied twice on one order, and the product attached to a reward is locked — only its quantity can be changed (consistent with Sales/Website loyalty).
- **Refunds give points back**, as they already do in Sales loyalty.
- **Gift cards / eWallets:** the customer popup now opens as soon as a top-up product is added with no customer; expired eWallets simply show a disabled button; a physical gift card keeps the program you picked first (no second prompt); gift card e-mails are logged in the POS order chatter.
- **Loyalty discounts are correctly reported** on POS reports.

## Technical data model changes

**Models**
- `res.partner.loyalty_card_count` removed.
- `loyalty.card.source_pos_order_partner_id` removed (use `source_pos_order_id.partner_id`).

**Fields**
- `pos.order.line.coupon_id` renamed to `card_id`; `reward_identifier_code` removed; new `gift_card_vals` (JSON) stores the gift card/eWallet program sold by a funding line until the card exists.
- `pos.order.applied_codes` (JSON) added, and `loyalty.card.source_pos_order_id` is now read-only with a null-friendly index.
- `loyalty.rule.any_product` and `promo_barcode` are now loaded into the POS.

**Methods (for custom developments)**
- `_load_pos_data_domain(self, data, config)` → `_load_pos_data_domain(self, data)` on `loyalty.card`, `loyalty.program`, `loyalty.reward`, `loyalty.rule`; `product.template._load_pos_data_search_read` replaced by `_load_pos_metadata`.
- `pos.config._get_program_ids(self)` → `_get_program_ids(self, check_usage=True)`.
- Removed: `pos.config._check_before_creating_new_session`, `use_coupon_code`; `loyalty.card._get_default_template`, `_get_signature`, `_mail_get_partner_fields`, `get_loyalty_card_partner_by_code`; `loyalty.reward._get_discount_product_values`; and the whole POS coupon flow `validate_coupon_programs`, `confirm_coupon_programs`, `_check_existing_loyalty_cards`, `_process_existing_gift_cards`, `add_loyalty_history_lines`, `_add_mail_attachment`.
- Added: `loyalty.card.get_card_status`, `_get_or_create_pos_card`, `_is_new_receipt_coupon`; `pos.order.read_pos_data`, `_process_loyalty`, `_process_saved_order`, `_get_loyalty_cards_to_credit`, `_get_refund_reversal_cards`, `_get_refund_reversal_points`, `_get_mail_attachments`; point-computation helpers on `loyalty.program`, `loyalty.reward` and `loyalty.rule`.

## How your habits should change

- Train staff: free products are now **redeemed from the Rewards buttons**, not automatically; a zero-price line is normal and intended.
- Stop telling users to "reset rewards"; reward lines are removed like any other line.
- One reward per product/per order more strictly than before — if a customer genuinely needs two identical discounts, split the order.
- Accountants/report writers: discount reporting on reward lines is now reliable.
- **Points are recomputed on the server when the order is validated.** The POS still computes rewards live so it keeps working offline, but the backend re-derives earned points and consumed points from the order lines, and creates the loyalty history itself. A manipulated client can no longer grant itself points.
- Selling gift cards/eWallets still requires a short RPC call at validation; everything else works offline.
- Any customisation touching `coupon_id`, `reward_identifier_code`, `use_coupon_code` or `confirm_coupon_programs` must be rewritten.

## What you gain by migrating

- A single, coherent loyalty engine shared by Sales and POS: same reward rules, same refund behaviour, fewer surprises for multi-channel customers.
- Server-side recomputation of points and history means trustworthy figures and auditable gift cards.
- Cleaner UX: no hidden automatic lines, no reset button, explicit claiming.
