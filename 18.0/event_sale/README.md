# event_sale migration guide (17.0 -> 18.0)

This addon bridges the Events and Sales apps: it sells event tickets as products, creates registrations from confirmed sale order lines and keeps attendees in sync with the order. In 18.0 the addon was slimmed down and the way a ticket product is defined changed.

## What's new for users

The 18.0 release notes cover Events and Sales broadly, but no entry describes a new screen inside event_sale. Two items matter for this addon:

- Product form reworked (Sales): Storable and Consumable product types are replaced by Goods, and the Tracked field decides how a product is tracked. This is why event tickets are no longer a product type of their own.
- Sale order lines: products and their descriptions now share a single column when editing lines, including event ticket lines.

Everything else in the release notes (Events from Point of Sale, pop-up registration button, visibility settings, retroactive leads) belongs to other apps and does not change how tickets are sold or registered here.

## Technical data model changes

- product.template: the `event`/`event_booth` values of `detailed_type` are gone (the field disappears in 18.0). Event behaviour is now carried by `service_tracking`; its `event` value ("Event Registration") and the matching onchange (`invoice_policy = 'order'`) now come from the new shared `event_product` module.
- event.type.ticket: event_sale no longer inherits it. `product_id`, `currency_id`, `price`, `price_reduce`, `description` and their computes, plus the `_init_column` and `_get_event_ticket_fields_whitelist` overrides, moved out.
- event.event.ticket: `price_reduce_taxinc`, `price_incl`, their computes and the `_compute_sale_available` override (product archived) moved out.
- event.event: `currency_id` (related to the company currency) removed.
- product.product: `event_ticket_ids` moved out.
- Ticket product domain: `[('detailed_type', '=', 'event')]` becomes `[('service_tracking', '=', 'event')]`.
- sale.order / sale.order.line: every `product_type`/`detailed_type == 'event'` test becomes `service_tracking == 'event'` (registration creation, the "needs an event and a ticket" constraint, partner updates, and the catalog domain hiding event products from the Sales catalog).
- Modules split: models/product.py becomes product_product.py + product_template.py; the `_detailed_type_mapping` override is gone.

## How your habits should change

- Stop looking for an "Event Ticket" product type. Create a Service product and set Service Tracking to Event Registration; the invoice policy is set to Ordered quantities for you. Existing 17.0 event products are converted during the upgrade — check your ticket products afterwards.
- Ticket setup is otherwise unchanged: price still comes from the product's sales price and the description from its sale description. Those fields simply live in `event_product`, now a dependency of event_sale.
- Sale order lines still require an event and a ticket for event products; the validation error is the same.
- Update your filters, exports, automated actions and reports that used `detailed_type = 'event'` to use `service_tracking = 'event'`.

## What you gain by migrating

- A cleaner product model aligned with the 18.0 product rework: one less product type, no obsolete `detailed_type`.
- The product ↔ event link now lives in a shared `event_product` module, reusable by other apps without installing the whole Sales suite: less duplicated code, fewer customization conflicts.
- No manual data migration: the upgrade handles the product type mapping, and your events, tickets and registrations keep working.
- You stay on a supported version with current security fixes and with OCA modules increasingly targeting 18.0.
