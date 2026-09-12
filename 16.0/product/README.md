# product migration guide (15.0 -> 16.0)

This guide covers the `product` addon (the base product catalog module) between Odoo 15.0 and 16.0. It is written for functional users and for a customer planning a 15.0 to 16.0 upgrade on the Community edition.

## What's new for users

The 16.0 release notes mostly highlight other apps (eCommerce, Sales, Inventory, Manufacturing, Website, Spreadsheet, Project...). For the `product` addon itself, the extract contains very little: the module is a foundation layer, and most product-related novelties appear in the apps built on top of it.

What actually concerns this addon:

- **Country of Origin**: a *Country of Origin* is now available on the product form, and is reused by shipping documents such as commercial invoices for international deliveries.
- **Product tags**: filtering products by tags is smoother, both in backend product lists and in the website shop.

Everything else in the extract (Amazon connector, coupons and loyalty, rental, MES, allocations, shipping carriers, accounting-style spreadsheet functions, etc.) belongs to other apps — and several of those items are Enterprise-only. Do not assume they come with a Community 16.0 upgrade.

## Technical data model changes

**Code reorganisation (no data impact)**
`product.py` was split into dedicated files: `product_product.py`, `product_category.py`, `product_packaging.py`, `product_supplierinfo.py`, with the module imports updated accordingly. The models and tables are the same. The `_inherits = {'product.template': ...}` and `mail.thread` declarations on `product.product` are unchanged: they only appear in the diff because the code moved between files.

Any custom module that patches file paths, overrides these models by file, or relies on `product.py` must update its references.

**Removed fields on `product.template`**
- `price` (float, context-dependent price computed from pricelist, quantity, uom, date), together with its methods `_compute_template_price`, `_compute_template_price_no_inverse` and `_set_template_price`.
- `pricelist_id` (non-stored technical field, used to search products by pricelist).

**Removed fields on `product.product`**
- `price` (same contextual price on the variant), with `_compute_product_price` and `_set_product_price`.

**Added methods**
- `_get_contextual_price()` on both `product.template` and `product.product`: returns the price for the context pricelist (context key `pricelist`), quantity, uom and date. It returns `0.0` when no pricelist is set in the context.

**Unchanged behaviour**: `list_price` (Sales Price), `price_extra` (variant extra), `standard_price`, `barcode`, `default_code`, images, packagings, vendor pricelists (`seller_ids`), categories and tags behave as before.

## How your habits should change

- The contextual **Price** and **Pricelist** columns disappear from product views. To get a customer- or quantity-specific price, use the price rules of the pricelist, or the product price list report.
- Saved filters, exports, automated actions, spreadsheet formulas or custom reports built on product `price` or `pricelist_id` will return nothing or fail. Review them before migrating and switch to pricelist data or `_get_contextual_price`.
- The old habit of typing a price through the contextual `price` field is gone: keep using **Sales Price** on the template and the variant extra prices.
- Any integration (website, connector, external tool) that wrote a price through `price` must be adapted.

## What you gain by migrating

- **Clearer product data**: pricing now always comes from pricelists and their rules, instead of a computed field that was context-dependent, often unused, and could show misleading values in lists and exports.
- **Fewer dead technical fields**: the non-stored `pricelist_id` search field is gone, which removes a common source of confusing search results.
- **Safer customisation**: the module is split into focused files, making your specific developments easier to review, patch and upgrade.
- **Preserved catalog**: products, variants, attributes, vendor prices, packagings, categories and tags migrate as-is; the changes are structural, not a re-modelling of your data.
- **Same functional flows, supported version**: you move to a maintained release while your sales, purchase, inventory and eCommerce flows keep relying on the same product foundation.

Plan a short audit of your customisations and reports that touch `price` / `pricelist_id`, then the upgrade itself is low risk for this addon.
