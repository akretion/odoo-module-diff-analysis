# sale_management migration guide (19.0 -> 20.0)

## What's new for users

The official 19.0 → 20.0 release notes extract contained no material specific to this addon. Everything below is drawn from the code-level changes shipped in `sale_management`.

**Quotation templates are now chosen at creation time.** Clicking *New* on a Sale Order now offers a template picker (same idea as the Project app). You can also edit or delete a template directly from that menu.

**The template field moved.** On a quotation, the "Quotation Template" field is renamed **Template** and now lives on the *Other Info* tab instead of the main header area.

**Productless (description-first) lines.** You can now write quotation and invoice lines from a description alone, without creating a product record. A new setting lets companies keep the old behaviour by making products mandatory. Productless lines can use any Unit of Measure and default to the company's sales taxes, and the Description field itself now supports searching products directly. Section templates fully support productless lines.

**Reusable section templates.** Save any section of a quotation as a reusable template (with price, discount and taxes preserved) and reapply it later. Currency conversions are handled when the template currency differs from the order.

**Quotation templates support the product configurator**, including variant attributes, and sections can now carry a quantity and UoM.

**Prepayment changes.** Down payments can now be a fixed **amount** as well as a percentage: entering one recalculates the other. On quotation templates only a percentage can be configured. The "Online Payment" toggle is gone — a prepayment of 0% now means "no prepayment required".

## Technical data model changes

**Removed**
- `res.company.sale_order_template_id` (the whole `res_company.py` model file is deleted) and `res.config.settings.company_so_template_id` with its `set_values()` override.
- `require_payment` on both `sale.order` and `sale.order.template`, with `_compute_require_payment()` and `_onchange_prepayment_percent()`.
- `sale.order._compute_sale_order_template_id()` and `_onchange_company_id()`.
- `sale.order.line._compute_name()` and `_use_template_name()`.
- `digest._compute_kpis_actions()` (replaced by `_get_kpi_custom_settings()`).

**Changed**
- `sale.order.sale_order_template_id`: label "Template", no longer computed and no `precompute=True`; it is no longer pulled from the company default.
- `sale.order.template.prepayment_percent`: label "Prepayment"; constraint relaxed to `0 <= value <= 1.0` and now driven by `company.portal_confirmation_pay`.
- `sale.order.template.line._prepare_order_line_values()` now takes `(fiscal_position, currency)`, and `sale.order.template.prepare_section_template_order_lines()` now takes `(order_changes, fiscal_position_id, company_id, currency_id, fields_spec)`.
- The accountable-line SQL constraint no longer requires a product, only a UoM.

**Added**
- `sale.order.template`: `currency_id`, `has_productless_lines`; `get_section_templates()`, `unlink_section_template()`, `_compute_user_has_access()`, `_search_user_has_access()`.
- `sale.order.template.line`: `discount`, `price_unit`, `tax_ids`, `mandatory_product`, plus configurator fields (`product_template_id`, attribute values) and `_compute_section_qty()` / `_compute_section_uom_id()`.
- `sale.order.line`: `save_section_template()`, `_prepare_template_line_values()`, `action_create_quotation_template()` on `sale.order`.

## How your habits should change

- Stop relying on a per-company **Default Template** setting; it has been removed. Pick the template when creating the order.
- Look for the template field under *Other Info*, not in the header.
- To disable prepayment on a template, set the prepayment percentage to 0 — there is no toggle anymore.
- If you want to keep enforcing a product on every line, enable the mandatory-product setting; otherwise expect to type descriptions freely.
- Start using *Save as section template* instead of copy/pasting recurring sections.
- Re-check any customisation or integration touching `require_payment`, the company default template, or `_prepare_order_line_values()`.

## What you gain by migrating

- Fewer clicks and a faster quote-to-cash path, thanks to the *New* button template picker.
- The flexibility to quote services, ad-hoc work and non-catalogued items without polluting your product database.
- Reusable sections and templates that preserve pricing, discounts and taxes, with proper multi-currency support.
- Down payments that fit real business practice (fixed amounts, not only percentages).
- A cleaner data model: one obvious place to choose a template instead of hidden company defaults.
