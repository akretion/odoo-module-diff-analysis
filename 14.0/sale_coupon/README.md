# sale_coupon migration guide (13.0 -> 14.0)

## What's new for users

- **Coupons & Promotions are now their own app.** Coupons and promotions can be activated on the Website even if you don't have the Sales app installed. In 13.0 the feature came with Sales.
- **Automatic coupon cleanup.** A cron now removes unused coupons applied on a Sales Order after three days, so abandoned promo codes no longer clutter draft orders.
- That is all the official 14.0 release notes say about this addon; the other Sales notes (Amazon, Incoterms, margins, pricelists, order tags) do not concern `sale_coupon`. The real 14.0 work here is structural — see below.

## Technical data model changes

14.0 performs a **module split**: `sale_coupon` no longer defines the coupon data model. The logic was renamed/moved into a new `coupon` app, and `sale_coupon` now builds on it.

- **No field was added** in this step and **no method signature changed** (report: 0 additions, 46 removed declarations, `method_signatures: NO`).
- These models/fields are no longer declared in `sale_coupon` (they now live in `coupon`, with the same table names):
  - `sale.coupon`: `code`, `expiration_date`, `state`, `partner_id`, `program_id`, `order_id`, `sales_order_id`, `discount_line_product_id`
  - `sale.coupon.program`: `name`, `active`, `rule_id`, `reward_id`, `sequence`, `maximum_use_number`, `program_type`, `promo_code_usage`, `promo_code`, `promo_applicability`, `coupon_ids`, `coupon_count`, `order_count`, `company_id`, `currency_id`, `validity_duration`
  - `sale.coupon.reward`: `reward_description`, `reward_type`, `reward_product_id`, `reward_product_quantity`, `discount_type`, `discount_percentage`, `discount_apply_on`, `discount_specific_product_ids`, `discount_max_amount`, `discount_fixed_amount`, `reward_product_uom_id`, `discount_line_product_id`
  - `sale.coupon.rule`: `rule_date_from`, `rule_date_to`, `rule_partners_domain`, `rule_products_domain`, `rule_min_quantity`, `rule_minimum_amount`, `rule_minimum_amount_tax_inclusion`
- The files `sale_coupon.py`, `sale_coupon_program.py`, `sale_coupon_reward.py` and `sale_coupon_rules.py` are gone from `sale_coupon/models/`, and `models/__init__.py` no longer imports `mail_compose_message` nor `sale_order`: the sales-order integration (promo code, applied coupons) is now carried by the `coupon` app.
- Because model and table names are unchanged, existing programs, coupons, codes and their history are preserved. Customisations (inherited models, Studio fields, server actions, reporting on these tables) still work, but must be re-tested against the new layout.

## How your habits should change

- **Enabling the feature**: promotions are now enabled through the new coupon app; Sales is no longer required for coupons on the Website.
- **Order housekeeping**: unused coupons on a sales order disappear automatically after three days via the cron — you can stop clearing them manually.
- **Daily usage is unchanged**: same program types (promotion / coupon), same rules (dates, customers, products, minimum amount and quantity), same reward types (percentage or fixed discount, free product) and same coupon codes.
- **Administrators / developers**: any custom module importing `sale_coupon.models.*` or relying on the old file layout must be re-pointed to `coupon`. Install/update the `coupon` app before `sale_coupon`, and test custom coupon rules after the upgrade.

## What you gain by migrating

- A **cleaner, reusable promotion engine**: coupons live in their own app, so they work on the Website without installing Sales.
- **Less housekeeping**: abandoned coupons on orders are cleaned up automatically.
- **No data loss**: database tables keep the same names, so programs, coupons, usage counters and reports carry over.
- **A maintainable code base**: staying on 13.0 means keeping a monolithic coupon implementation that Odoo no longer evolves; 14.0 gives the same features on the new, supported structure.
- **A good moment for an audit**: if you have custom developments around coupons, the split makes them easier to maintain once re-pointed to the new module — a short review is needed, but it is a one-off.
