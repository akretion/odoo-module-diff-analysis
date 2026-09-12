# sale migration guide (17.0 -> 18.0)

## What's new for users

- **Quotation onboarding revamped**: the guided onboarding steps are removed, replaced by a new tour, a clearer "no quotation yet" message and a preview video of the PDF quote builder.
- **Combo products in Sales**: sell several products as a single line, with options the customer can pick from.
- **EDI for orders**: drag & drop a customer purchase order file on the Sales app to create a pre-filled quotation.
- **Quotation templates**: product descriptions defined on a template are reused in quotations; templates can be sequenced.
- **PDF quote builder**: more flexible (when to use it, which documents, per-quote custom text); any model field, including customised ones, can be used.
- **Quotation follow-up**: a salesperson can be notified when the customer views the quotation in the portal, and that customer is suggested as a follower.
- **Line editing**: products and descriptions now share a single column; a manually entered unit price is no longer recomputed when the quantity changes; archived products are flagged on the order.
- **Products**: Storable/Consumable become Goods, tracking is driven by the Tracked field; attribute values can be deleted and attribute changes applied to all products.
- **Pricelists**: simplified views plus PDF/CSV/XLSX print formats.

## Technical data model changes

- `sale.order.analytic_account_id` is removed. Analytic follow-up now relies on the project set on the order (and the analytic distribution of its lines); expense re-invoicing is based on that project.
- The `onboarding.onboarding` and `onboarding.onboarding.step` overrides are deleted from the addon.
- New computed fields and helpers: `has_archived_products`, `duplicated_order_ids`, `is_product_archived`.
- New methods on sale.order.line: `_get_downpayment_description`, `_compute_amount_to_invoice`, `_compute_amount_invoiced`, `_compute_qty_invoiced_posted`, `_can_be_invoiced_alone`, `_add_precomputed_values` (keeps a manually edited price), `_get_linked_line(s)`, `_check_combo_item_id` (combos), `_sellable_lines_domain`, `_get_custom_compute_tax_cache_key`.
- Changed signatures: `_default_order_line_values(child_field=False)`, `_get_product_catalog_record_lines(product_ids, **kwargs)`, `_notify_by_email_prepare_rendering_context(..., msg_vals=False)`.
- Removed methods: `_can_be_confirmed` (replaced by `_confirmation_error_message`), the TaxCloud hooks, `_create_analytic_account` / `_prepare_analytic_account_data`.
- account.move gains credit-limit helpers; payment.transaction `_post_process` replaces `_reconcile_after_done`, `_set_authorized` and `_set_pending`.
- Good news: the sale/purchase `account.order.mixin` refactor and the `invoice_lines` → `account_move_line_ids` rename were **reverted before release**. Field and method names from 17.0 remain valid.

## How your habits should change

- Skip the old onboarding panel; onboard new users through the standard quotation flow and the new tour.
- Track profitability on the order's Project (or line analytic distribution) instead of an analytic account on the sales order.
- "Re-Invoice Expenses" is now "Re-Invoice Costs" and also covers vendor bills and stock pickings tracked on projects; it appears for purchasable products only.
- Trust your price edits: changing the quantity no longer resets the unit price.
- Use "Goods" and "Tracked" instead of "Storable"/"Consumable".
- When a customer sends an electronic order, create the quotation by dropping the file in.

## What you gain by migrating

- Less manual entry: combo sales, EDI import, quotation templates with ready-made descriptions.
- Cleaner cost tracking: projects carry analytic plans, so expenses, vendor bills and stock are followed and re-invoiced automatically.
- Fewer mistakes: sticky unit prices, archived-product warnings, no auto-invoice on zero-amount orders, duplicate order detection.
- Better customer-facing documents: revamped PDF quote builder and pricelist exports.
- A low-risk upgrade: the risky core sales-order refactoring was reverted, so there is no field rename to chase in your customisations.
