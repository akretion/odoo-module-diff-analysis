# sale_stock migration guide (19.0 -> 20.0)

`sale_stock` links Sales orders to Inventory: lead times, reservations, availability and delivery. This guide summarises what changes between 19.0 and 20.0 for functional users.

## What's new for users

**Day-based delays are now whole days.** Customer lead time, sales safety days (the "Security Lead Time" setting) and the other planning delays no longer accept decimals. Fractional days were never meaningful for a date computation, so Odoo now enforces integer values. The fields look and behave the same on screen - only the accepted values change.

**A stock availability widget now exists in Sales, without Inventory.** On relevant quotation/sales order lines, the Sales app now shows a simplified availability widget: the current quantity on hand and the forecasted quantity at the commitment date of the order. It answers "can I sell now, and do I need to order more?" without installing Inventory. With `sale_stock` installed, sales order lines keep the full stock-linked information (warehouse, routes, quantity to deliver, forecast date).

**Customer lead time per company.** A product's customer lead time can now differ from one company to another.

**Fulfilling without Inventory.** The Sales app can now confirm and ship orders, and send shipping confirmation emails, without the Inventory app; `sale_stock` remains the addon to use when real stock, reservations and delivery orders are involved.

The official 20.0 release notes contain no dedicated `sale_stock` section; the items above are the only ones relevant to this addon.

## Technical data model changes

Day-based delay fields converted from Float to Integer:

- `res.company.security_lead` ("Sales Safety Days"): Float -> Integer, required, default 0 (was 0.0).
- `res.config.settings.security_lead` ("Security Lead Time", related to `company_id.security_lead`, readonly=False): Float -> Integer.
- `sale.order.line.customer_lead`: Float -> Integer (still computed, stored and editable; `_compute_customer_lead` and `_inverse_customer_lead` unchanged).
- The same conversion applies to the companion modules changed by the same commit (sale, stock, purchase_stock): replenishment horizon and days to purchase.

Fields removed from `sale.order.line` in `sale_stock`:

- `virtual_available_at_date` (Float, computed)
- `scheduled_date` (Datetime, computed)
- `qty_available_today` (Float, computed)
- `is_storable` (Boolean, related to `product_id.is_storable`)

Fields still provided on `sale.order.line` by `sale_stock`: `move_ids`, `route_ids`, `warehouse_id`, `forecast_expected_date`, `free_qty_today`, `qty_to_deliver`, `is_mto`, `display_qty_widget`, `customer_lead`.

No model was added or removed. Upgrade scripts are shipped upstream (odoo/upgrade#9188 and #9081).

## How your habits should change

- Enter whole days in customer lead time and sales safety days; existing decimal values are converted by the upgrade script.
- Adapt custom reports, exports, server actions or integrations reading `security_lead` / `customer_lead` as decimal numbers: they now receive integers.
- Review custom views, filters, QWeb reports and automated actions referencing `is_storable`, `qty_available_today`, `virtual_available_at_date` or `scheduled_date`: these fields no longer exist on sales order lines in `sale_stock`. Use the remaining stock fields or the base Sales availability data instead.
- Glance at the availability widget on the order line to confirm you can deliver at the commitment date before confirming a quotation.
- The sales settings and product-level lead times keep their usual location and wording.

## What you gain by migrating

- Unambiguous planning: no stray decimals in promised dates, safety days or replenishment horizons.
- A shared availability indicator between Sales and Inventory, visible earlier in the sales flow, even for teams that do not use the Inventory app.
- Company-specific customer lead times for multi-company organisations.
- A supported migration path: official upgrade scripts convert your existing delay data, so the change stays transparent in day-to-day work.
