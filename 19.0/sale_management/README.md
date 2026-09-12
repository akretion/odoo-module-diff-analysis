# sale_management migration guide (18.0 -> 19.0)

## What's new for users

**Optional products become optional sections.** In 18.0, optional products lived in a dedicated "Optional Products" tab. In 19.0 the concept is unified with sections: any (sub)section of a quotation can be flagged as optional from the section dropdown. As stated in the release notes, portal users can now set quantities for those lines directly in the portal, and admins keep the same ability. A line in an optional section with quantity 1 counts as opted in, and flagging a section as optional sets its lines to quantity 0.

**Quotation templates follow the same logic.** Optional items are no longer a separate list on the template: the template simply contains optional sections holding their product lines.

**Units of measure are simplified.** UoM categories have been removed (Inventory release notes: product units and packagings are merged into one simplified model). On template lines you now choose among the units allowed for the product — its reference unit plus its packagings — instead of being locked to a category.

The rest of the 19.0 extract (industry packages, localizations, Point of Sale, connectors, payroll, etc.) concerns other apps or Enterprise-only features and is out of scope here.

## Technical data model changes

Removed models: `sale.order.option` and `sale.order.template.option`.

Removed fields: `sale.order.sale_order_option_ids`, `sale.order.line.sale_order_option_ids`, `sale.order.template.sale_order_template_option_ids`, and `product_uom_category_id` (on template lines and on the removed option models).

Added fields:
- `sale.order.line.is_optional` (Boolean, copied)
- `sale.order.template.line.is_optional` (Boolean, copied) and `parent_id` (computed Many2one to the parent section/subsection)
- `allowed_uom_ids` (computed Many2many) on `sale.order.template.line`; the `product_uom_id` domain now filters on it.

Method signature changes: `sale.order.template.line.write(values)` -> `write(vals)`; `_prepare_option_line_values` now forwards `is_optional`; new `sale.order._onchange_partner_id` (refresh the quotation template when the customer changes on an unsaved, unmodified quotation); new helper `sale.order.line._is_line_optional`.

Changed behaviour: the optional pricing override is gone (`_compute_price_unit` and `_lines_without_price_recomputation` on `sale.order.line`, `_recompute_prices` on `sale.order`); the multi-company constraint `_check_optional_product_company_id` is removed from `sale.order` (the template constraint now only inspects template lines); `sale.order._can_be_edited_on_portal` is removed and portal editability is driven by `is_optional`.

## How your habits should change

- No more "Optional Products" tab, neither on quotations nor on templates.
- To offer an option, add a section (or subsection) in the order lines and mark it optional from its dropdown. Lines under it start at quantity 0.
- To opt a customer in, set the quantity of the relevant lines. Portal customers can do it themselves on draft/sent quotations.
- In templates, build the optional offer as a regular section plus lines carrying the optional flag, with quantities at 0.
- The unit of measure on a template line is no longer restricted by category; use the product's units and packagings.
- Existing 18.0 data is converted to optional sections by the upgrade scripts (odoo/upgrade#8211): review your templates after migrating.

## What you gain by migrating

- One single model for products and options: fewer tabs, less duplication, faster quotation building.
- Customer self-service: portal users pick their own options inside the quotation.
- Simpler, more flexible unit-of-measure handling when you buy and sell in different units.
- Cheaper template maintenance: no separate optional product list to keep in sync.
- Bug fixes carried over: template refresh on partner change, multi-company checks.
