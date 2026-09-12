# sale migration guide (14.0 -> 15.0)

This guide covers the Odoo `sale` addon (Community edition) between 14.0 and 15.0: what changes for users, what changes in the data model, and what you actually gain.

## What's new for users

The official 15.0 release notes only mention a few sales-related items. Those that concern the `sale` addon in Community are:

- **Pricelists**: the pricelist item form views are improved and include a simulation, so you can check the effect of a rule before saving it. This is where you build and verify your sales prices.
- **Terms & Conditions**: default content now lives on a web page, and quotations/orders carry a default link to it instead of duplicating the legal text on every document. You no longer maintain the same text manually on each quotation.
- **Taxes on invoices**: tax groups can be broken down on invoices to display an intermediate subtotal (handy with discounts or withholding taxes). You will see this on the invoices you generate from sales orders.
- **Accrued entries**: entries such as "Invoices to be Issued" can be generated from sales order actions, which helps at period closing (Accounting app).

The rest of the release notes (Lead Scoring, Outlook/Gmail connectors, Planning, POS, eCommerce, Inventory, Email Marketing, most localizations) belongs to other applications or to Enterprise editions — do not expect them from this migration.

## Technical data model changes

Based on the 14.0 → 15.0 diff of the addon, **no model, field or relation table of the sale application was added, removed or made mandatory**. The two commits touching `sale` are declared as pure code moves (`[MOV]`):

- `models/sale.py` was split into `models/sale_order.py` (`sale.order`) and `models/sale_order_line.py` (`sale.order.line`).
- `models/payment.py` was split into `models/payment_acquirer.py` (`payment.acquirer`, incl. the `so_reference_type` field) and `models/payment_transaction.py` (`payment.transaction`, with `sale_order_ids`, `sale_order_ids_nbr` and the automatic confirmation/invoicing logic).

Consequences:

- Key fields are unchanged, e.g. on `sale.order`: `name`, `state`, `date_order`, `order_line`, `amount_total`, `invoice_status`, `transaction_ids`; on `sale.order.line`: `product_id`, `product_uom_qty`, `qty_delivered`, `qty_invoiced`, `qty_to_invoice`, `price_subtotal`. Relation tables (`sale_order_transaction_rel`, `sale_order_line_invoice_rel`) keep their names. **No database migration script is required for these models.**
- The diff tool reports ~99 "deleted" fields for `sale.order`/`sale.order.line` and 6 for the payment models: these are artefacts of the file split — the fields moved file, they were not deleted.
- No method signature change is reported for the addon.
- The real impact is on customizations: any module importing `odoo.addons.sale.models.sale` must now import `...models.sale_order` or `...models.sale_order_line`, and `sale.models.payment` no longer exists. Model names and XML data (views, templates, actions) are unchanged, so standard xpath-based view customizations and field-level code overrides keep working.

## How your habits should change

Day-to-day selling is unchanged: same quotation form, same confirmation, delivery and invoicing flow.

What is worth adjusting:

- **Terms & Conditions**: after migration, review the T&C web page and the company setting controlling whether terms are used, so quotations link to the right content. The `note` field on an order now typically holds a link rather than the full text.
- **Pricelists**: take a moment to rediscover the item form and its simulation before tweaking rules; the layout changed, not the pricing behaviour.
- **Invoice tax display**: invoices created from sales orders may show a subtotal per tax group — useful, but check your invoice templates if you customized them.
- **Technical habits (for your integrator)**: update imports in custom modules and re-test any module inheriting `sale.models.sale` or `sale.models.payment`.

## What you gain by migrating

- A sale codebase split by model: easier to read, patch and upgrade, with fewer conflicts for your custom modules and a shorter path to future versions.
- No data migration burden on sales data: same models, same fields, same relations.
- New pricelist configuration with simulation, Terms & Conditions maintained once as a web page, and clearer tax breakdown on invoices.
- A faster upgrade overall: because the sale addon's stored data model is untouched between 14.0 and 15.0, migration effort concentrates on customizations and on the other apps (Accounting, Inventory) rather than on sales records.
