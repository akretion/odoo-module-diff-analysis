# product migration guide (15.0 -> 16.0)

Scope: the `product` addon only (the base module for products, variants, pricelists and units of measure). Applications built on top of it are covered by their own guides.

## What's new for users

The 16.0 release notes are mostly about the applications that *use* products (eCommerce, Sales, Inventory, Purchase, Website). Only one product-centred item really belongs to this addon:

- **Product tags** – classify products with tags and filter them easily, in the backend and on the website.

Everything else you may read about products in the release notes (Shop page redesign, product pictures grid/carousel, default sorting, coupons and loyalty cards, back-in-stock notifications, B2B "hide prices", product comparison prices) is delivered by other addons such as Website/eCommerce, Sales or Loyalty, and some of it is Enterprise-only. Nothing else in `product` changes noticeably for day-to-day users: creating products, variants, attributes, pricelists and units of measure works the same way as in 15.0.

## Technical data model changes

From the 15.0 → 16.0 code diff:

**Removed fields**
- `product.template.price` – computed Float "Price", context dependent (pricelist, partner, quantity, uom, date).
- `product.template.pricelist_id` – non-stored Many2one, technical field used only for searching on pricelists.
- `product.product.price` – same context-dependent computed Float, on the variant.

They were removed because they were unused, did not behave correctly, and duplicated what pricelist rules already compute. The inverse methods that wrote their value back into `list_price` are gone as well.

**Unchanged**
- `list_price` ("Sales Price") on the template remains the user-defined catalogue price; standard pricelist computation is untouched.

**Added**
- `_get_contextual_price()` on both `product.template` and `product.product`. It returns `0.0` when no `pricelist` is present in the context; otherwise it reads `quantity`, `uom` and `date` from the context and returns the price computed by the pricelist. Unlike the old `price` field, it is read-only: it never writes to `list_price`.

**Code structure**
- The model files were reorganised (`product.py` split into `product_product.py` / `product_template.py`). No data impact, but custom code that patches file paths must be updated.

## How your habits should change

- Any custom export, import, list view, filter, server action or report referencing `price` or `pricelist_id` on products must be rewritten: those fields no longer exist.
- To know the price of a product for a given customer, quantity, unit of measure and date, use the pricelist price report (Pricelists, or the "Product Price" report) or a pricelist rule, instead of the old contextual "Price" column.
- Developers: replace `product.with_context(pricelist=..., quantity=...).price` with `product._get_contextual_price()` or, preferably, with the pricelist's price computation method. Remember that no value is written back to Sales Price anymore.
- Day-to-day sales, purchases, eCommerce and POS behaviour is unchanged: prices still come from pricelists, so quotations, sales orders and carts are not affected.
- If you adopt product tags, tag your catalogue to benefit from the new filters and website filtering.

## What you gain by migrating

- **One single source of truth for prices**: pricelist rules. The old context-dependent "Price" field could return different values depending on context and could silently overwrite your Sales Price – that risk is gone.
- **Fewer surprises in customisations**: no ghost non-stored field that ends up by mistake in list views, exports or reports.
- **A cleaner, better maintained code base** (models split per file), which makes future customisations and upgrades cheaper and safer.
- **Product tags** to organise and filter a large catalogue, plus all 16.0 improvements brought by the modules that build on `product`.
- Staying on an up-to-date, supported Odoo version, with the fixes and new features delivered by the dependent apps.
