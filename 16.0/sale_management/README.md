# sale_management migration guide (15.0 -> 16.0)

`sale_management` (Community) is the addon that adds **quotation templates** and **optional products** to the Sales app: reusable templates with lines, optional products, quotation duration, terms & conditions, and online signature/payment flags.

## What's new for users

The official 16.0 release notes say nothing specific about quotation templates or optional products — the "Sales" entries (delivery status, loyalty/coupons, Amazon, pricelist warnings, etc.) belong to other modules, some Enterprise-only. There is therefore no new screen to discover here; the value lies in harmonised, more predictable behaviour:

- Selecting a quotation template on a quotation/order now works the same in the backend and on the website: the template is a stored, editable computed field instead of default/onchange logic.
- Template-driven values (validity date, online signature, online payment, terms & conditions) are computed from the selected template while the order is still editable.
- Description and Unit of Measure on template lines and optional products are auto-filled from the product, but remain editable.
- Multi-company safety: a product restricted to one company can no longer be added to a template shared by several companies. Previously, adding such a product silently changed the template's company; now the product selection is simply filtered.

## Technical data model changes

**sale.order** (inherited):
- `sale_order_template_id` becomes a stored, editable computed field (`_compute_sale_order_template_id`, depends on `company_id`, precompute); the `default_get()` override is removed.
- Onchange renamed: `onchange_sale_order_template_id` → `_onchange_sale_order_template_id` (now private).
- `copy()` override removed; `_compute_validity_date`, `_compute_require_signature`, `_compute_require_payment` and `_compute_note` now derive their values from the template.
- `sale_order_option_ids` is no longer `readonly=True` (readonly driven by states only).

**sale.order.template**:
- `require_signature` / `require_payment` become computed+stored, editable, based on the template company's portal confirmation settings (previously defaults).
- Onchange `_onchange_template_line_ids` removed (it used to set the company from the products).

**sale.order.template.line**:
- `product_id` is now `required=True`, with a domain restricted to sale-ok products of the template company (or shared ones).
- `name` and `product_uom_id` become computed+stored, editable, precompute; onchange `_onchange_product_id` removed.
- SQL constraints kept.

**sale.order.template.option**:
- `product_id` domain restricted to the template company; onchange `_onchange_product_id` removed; `name` / `uom_id` computed+stored, editable.
- `sale.order.option._get_values_to_add_to_order()` no longer returns `company_id`; field attributes and ordering cleaned up.

## How your habits should change

- Everyday template use is unchanged: pick a template, lines and optional products are loaded.
- Set the company on the template **before** adding products: company-restricted products are filtered out rather than moving the template to their company.
- Because validity date, signature/payment flags and note are stored computed fields, editing a template record does not retroactively rewrite quotations already created — change the template on the order if needed.
- If you customised the addon: update calls to the renamed onchange, drop code relying on the removed `default_get()`/`copy()` overrides, and make sure every non-section template line has a product and a unit of measure.

## What you gain by migrating

- Identical template behaviour in Sales and on the website: fewer surprises for salespeople and customers.
- Stronger multi-company rules — no silent company reassignment of shared templates.
- Cleaner template data: product required, UoM always consistent with the product, descriptions always meaningful.
- A maintained, refactored codebase aligned with 16.0 sale computes (prices and discounts delegated to standard Sales logic), which makes future upgrades smoother.
