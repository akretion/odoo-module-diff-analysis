# sale migration guide (19.0 -> 20.0)

Migration guide for the Community addon `sale`. No 20.0 release-note extract matched this addon, so everything below is derived from the code diff (data model and method changes) between 19.0 and 20.0.

## What's new for users

**Services & Materials view.** A new view on the sale order lets you record extra delivered services or consumables directly as analytic lines, without first creating an order line. Depending on the product's re-invoicing policy, Odoo creates a new order line (at cost) or updates an existing one (sales price), keeps delivered quantities in sync, and reverts the line if you delete the entry. A stat button on the invoice lists the analytic lines linked to it.

**Description-first (productless) sale order lines.** Order lines can be written from a description alone; the description field also works as a searchable product + description field. A setting keeps the strict "product required" behaviour if you prefer it. Productless lines default to `Units` and to the company's default sales taxes, and are supported in section templates, loyalty rewards, sales reports and commissions.

**Fixed-amount prepayments.** Besides a percentage, you can now request a fixed prepayment amount before confirmation; editing either value recalculates the other.

**Other user-visible additions:** manual close/reopen of invoicing on an order, quantities on sections, a % delivered indicator, invoice overages, an expiry filter on quotations, default incoterm taken from the contact, delivery and availability handling for orders without stock, taxes recomputed when the fiscal position changes, prices recomputed when the pricelist changes, and a catalog filter for products previously bought by the customer.

## Technical data model changes

- `product.template`: `expense_policy` renamed to `reinvoice_policy`; `visible_expense_policy` -> `visible_reinvoice_policy`.
- `sale.order`: `require_payment` removed (a prepayment is now expressed by `prepayment_percent > 0`); `prepayment_amount` added; `analytic_account_id` added.
- `res.company`: `sale_invoice_policy` and `sale_automatic_invoice` added; `prepayment_percent` now accepts 0.
- `sale.order.line`: `invoice_policy` became a computed/searchable field; `mandatory_product` added; new line-type helpers (`_is_product_line`, `_is_discount_line`, `_is_downpayment`, `_is_reinvoicing_line`); the model now inherits the new `product.catalog.line.mixin`.
- `account.analytic.line`: `so_line` and `order_id` are now stored computed fields; `timesheet_invoice_id` becomes `reinvoice_move_id`; synchronisation helpers (`_sync_so_lines`, `_unsync_so_lines`, ...) centralise re-invoicing.
- `account.move`: `service_line_count` and `action_view_services_analytic_lines` added.
- `crm.team`: `invoiced` and `invoiced_target` removed.
- `utm.campaign`: `company_id` removed; `currency_id` is computed from the environment company; `invoiced_amount` becomes Monetary and is converted with exchange rates.
- Signature changes: `_create_invoices(final, grouped)`, `_prepare_analytic_account_data(prefix, plan_id)`, `_check_amount_and_confirm_order` -> `_confirm_order`, plus mail notification/tracking API updates.

## How your habits should change

- Templates only accept a **prepayment percentage**; on a quotation you may switch to an amount. Set it to 0 to drop the online prepayment requirement — the "Online payment" toggle no longer exists.
- Replace every reference to `expense_policy` (imports, exports, custom code, automated actions) with `reinvoice_policy`.
- "Automatic Invoicing" is now a **per-company** setting instead of a system parameter: review it company by company after the upgrade.
- The sales team kanban revenue/target fields and their progress bar are gone; if you relied on them for reporting, plan an alternative.
- UTM campaigns no longer carry a company; their displayed currency follows the company you are working in.

## What you gain by migrating

- Re-invoicing is now captured in one place: extra services and materials are entered on the order and delivered quantities stay consistent with what has been invoiced, so at-cost or sales-price re-invoicing cannot drift.
- Faster quoting for simple lines, with no need to create a product for every line.
- More flexible cash collection through fixed-amount prepayments.
- Fewer manual steps in daily work: taxes and prices react automatically, incoterms come from the contact, and invoicing can be closed or reopened by hand.
- More reliable figures: invoiced totals are converted with exchange rates instead of being added up blindly.
- You stay on a maintained codebase — remaining on 19.0 means no further fixes and growing divergence from the OCA modules you depend on.
