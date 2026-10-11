# sale_management migration guide (18.0 -> 19.0)

## What's new for users

**Optional products become optional sections.** In 18.0, quotations and templates had a separate "Optional Products" tab listing extra products the customer could add. In 19.0 you mark any section or subsection of the quotation as **Optional**; every product line under it becomes a suggestion, with quantity set to 0.

**Customers opt in from the portal.** Portal and internal users can edit the quantities of products inside an optional section directly on the quotation. Quantity 1 means opted in, 0 means not ordered. The per-option "Add to order" button is gone.

**More unit of measure flexibility.** Unit categories have been removed: on sale order lines and template lines you now choose among the units and packagings defined on the product itself.

The 19.0 release notes only echo this change for this add-on: "Editable optional products in portal - Sections or subsections of quotations can now be set as optional, with portal users able to select quantities for those lines directly in the portal." The other Sales notes cover other apps.

## Technical data model changes

**Removed models:** `sale.order.option` and `sale.order.template.option`.

**sale.order:** `sale_order_option_ids` removed; the `_check_optional_product_company_id` constraint removed (company consistency now follows standard line logic); the `_can_be_edited_on_portal` override and the `_recompute_prices` override removed; the template onchange no longer creates option records.

**sale.order.line:** `sale_order_option_ids` removed; new `is_optional` boolean (copied); new helper `_is_line_optional()` (True when the parent section, or the parent subsection's section, is optional); `_can_be_edited_on_portal` now delegates to it; `_compute_price_unit` override and `_lines_without_price_recomputation()` hook removed.

**sale.order.template:** `sale_order_template_option_ids` removed; `_check_company_id` now only inspects template lines; demo data uses an optional section with quantity-0 lines.

**sale.order.template.line:** new `is_optional` boolean (copied); new computed `parent_id` (parent section/subsection); `_prepare_order_line_values` propagates `is_optional` to the quotation line; `allowed_uom_ids` replaces `product_uom_category_id` for the unit domain.

**Units of measure:** `allowed_uom_ids` is computed from the product's reference unit and its packagings (`product.uom_ids`).

## How your habits should change

* **Templates:** build optional offers as a section or subsection in the **Lines** tab, flag it **Optional**, and put suggested products under it. The "Optional Products" tab no longer exists.
* **Quantities:** optional lines start at 0; raise them to 1 (or more) to order, 0 is not billed.
* **Portal:** customers edit ordinary quotation lines inside the optional section instead of clicking an add button per option.
* **Units:** choose the product's own units or packagings; the "same category as the product unit" rule is gone.
* **Migration:** an upgrade script accompanies this refactoring. Check your templates after migrating, as optional products become quantity-0 lines under optional sections.
* **Customisations:** anything touching `sale.order.option`, `sale.order.template.option`, `_lines_without_price_recomputation` or the removed constraint must be reworked.

## What you gain by migrating

* **One concept instead of two:** optional products are ordinary lines inside an optional section, so nothing needs synchronising.
* **Faster quotes:** customers set their own quantities in the portal, cutting back-and-forth.
* **Fewer clicks:** salespeople no longer duplicate products between order lines and an option table.
* **Flexible units:** sell in the unit that suits the deal, using the product's packagings.
* **Less custom code:** fewer deprecated fields and hooks to maintain at your next upgrade.
