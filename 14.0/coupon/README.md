# coupon migration guide (13.0 -> 14.0)

In 14.0 the coupon feature was extracted from the Sales app into a new, generic **`coupon`** addon; `sale_coupon` now simply extends it. Business features are unchanged, but the underlying models were renamed and the coupon logic was separated from sale-order logic.

## What's new for users

- Coupons & promotions are no longer tied to the Sales app: the new generic `coupon` module can be reused by other apps, and per the 14.0 release notes coupons and promotions can be activated on the Website even without the Sales app installed.
- Day-to-day screens (Promotions / Coupon Programs and the generated Coupons) work the same way as in 13.0. There is no new menu or setting to learn for this addon.
- The other 14.0 release-note items (Amazon, Incoterms, pricelists, order tags, website builder, etc.) belong to other apps and do not affect coupons.

## Technical data model changes

Models renamed (existing data is migrated by Odoo's upgrade scripts):

| Odoo 13.0 | Odoo 14.0 |
|---|---|
| `sale.coupon` | `coupon.coupon` |
| `sale.coupon.program` | `coupon.program` |
| `sale.coupon.rule` | `coupon.rule` |
| `sale.coupon.reward` | `coupon.reward` |

Fields removed:
- `coupon.coupon.order_id` ("Order Reference") and `coupon.coupon.sales_order_id` ("Used in"): the direct link between a coupon and the sales order that generated or consumed it no longer lives on the coupon; the Sales addon keeps that information.
- `coupon.program.order_count` (computed number of sales orders using the program) and the "Sales Orders" button that used it (`action_view_sales_orders`).

Methods moved out of `coupon.program` into `sale_coupon` (they are order-specific): `_check_promo_code`, `_check_coupon_code`, `_filter_on_mimimum_amount`, `_filter_on_validity_dates`, `_filter_promo_programs_with_code`, `_filter_unexpired_programs`, `_filter_programs_on_partners`, `_filter_programs_on_products`, `_filter_not_ordered_reward_programs`, `_filter_programs_from_common_rules`, `_is_global_discount_program`, `_keep_only_most_interesting_auto_applied_global_discount_program`.

Behaviour changes:
- `_compute_expiration_date` is now a real computed field (`@api.depends('create_date', 'program_id.validity_duration')`), so expiration dates are refreshed when the program validity changes.
- Deleting a program (`coupon.program.unlink`) now also deletes its rule and reward records instead of leaving them behind.
- The discount product auto-created with a program is no longer created with the invoice policy "Ordered quantities".
- The coupon email template XML id is now `coupon.mail_template_sale_coupon` (was `sale_coupon.mail_template_sale_coupon`).
- New `mail.compose.message` override: when the coupon email is sent, the coupon is automatically set to "Sent".
- The coupon expiry cron now queries the new `COUPON_COUPON` / `COUPON_PROGRAM` tables.

## How your habits should change

- No change for everyday coupon use: create programs, generate codes and apply them on orders exactly as before.
- If you use reports, saved filters, exports, automated actions, email templates or custom modules pointing at `sale.coupon*` models, they must be updated to `coupon.*`.
- The "Sales Orders" smart button on a promotion is gone; use the Sales Orders list (or the discount product) to find the orders that used a promotion.
- Deleting a draft promotion now removes its rule and reward as well: less manual cleanup, but no reusable rule/reward is left behind.
- Ask your integrator to review custom code and automation rules before going live.

## What you gain by migrating

- A cleaner, more future-proof architecture: coupons become a standalone, reusable feature that can be plugged into apps other than Sales (e.g. Website) — the direction Odoo keeps building on.
- Fewer leftovers: rules and rewards are deleted together with their program.
- Correct expiry dates when program validity changes, and automatic "Sent" status on emailed coupons.
- The user interface and the coupon workflow are unchanged, so the migration is mostly transparent for your teams while keeping you on a supported version.
