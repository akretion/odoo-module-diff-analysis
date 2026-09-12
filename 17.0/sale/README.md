# sale migration guide (16.0 -> 17.0)

## What's new for users

Scope: the Community `sale` addon; release-note items from other apps (Subscriptions, Loyalty, Amazon, Events, PoS) are out of scope.

- **Add products from catalog**: the catalog view used to quickly add products to a quotation is now shared with Purchase, so both apps use the same widget.
- **Down payments**: you can ask for one to validate a quotation; down payment lines now show the reference and date of the down payment invoice, and the original order's tax breakdown is respected. The partial-payment flow is smoother (easier payment links, automatic confirmation once partial payments cover the total).
- **Global discounts**: apply a discount to the whole sale order, not only line by line.
- **Sales order locked**: lock an order at any stage to prevent further edits.
- **Quotations mass cancellation**: cancel several quotations at once from the list view.
- **Sales without pricelists**: sales flows can now run without any pricelist.
- **Product documents**: documents linked to a product are sent automatically with the quotation, or when the order is confirmed.
- **PDF reports**: product lines with zero quantities are no longer printed in the variant table.
- **Credit limit**: confirmed, not-yet-invoiced sales orders count in the partner's receivables, for a more accurate warning.

## Technical data model changes

Onboarding (sale onboarding progress left `res.company`):
- Removed fields on `res.company`: `sale_quotation_onboarding_state`, `sale_onboarding_order_confirmation_state`, `sale_onboarding_sample_quotation_state`. `sale_onboarding_payment_method` remains.
- Progress is now stored with the dedicated `onboarding.onboarding` / `onboarding.onboarding.step` models, extended by sale.
- Removed methods on `res.company`: `action_close_sale_quotation_onboarding`, `action_open_sale_onboarding_payment_provider`, `action_open_sale_onboarding_sample_quotation`, `_get_sample_sales_order`, `_mark_payment_onboarding_step_as_done`, `_get_and_update_sale_quotation_onboarding_state`.
- New entry points: `onboarding.onboarding.action_close_panel_sale_quotation`; `onboarding.onboarding.step.action_open_step_sale_order_confirmation`, `action_open_step_sample_quotation`, `action_validate_step_payment_provider`.

Product catalog moved from `sale` to `product`:
- New mixin `product.catalog.mixin`; `sale.order` now inherits it.
- New methods on `sale.order`: `_default_order_line_values`, `_get_action_add_from_catalog_extra_context` (no argument; the order builds its own context), `_get_product_catalog_order_data`, `_get_product_catalog
