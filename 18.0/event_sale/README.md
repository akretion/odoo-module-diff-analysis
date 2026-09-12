# event_sale migration guide (17.0 -> 18.0)

This guide summarizes what changes for the *event_sale* addon when moving from Odoo 17.0 to 18.0. Most of the change is a product-model cleanup: "Event Ticket" is no longer a product type of its own.

## What's new for users

- **Event ticket products are configured differently.** In 18.0 you create a *Service* product and set **Service Tracking = Event Registration**. The old dedicated "Event Ticket" product type is gone. A tooltip on the field explains what the option does.
- **Ticket links in the order confirmation email.** The confirmation mail sent to attendees now includes links to their tickets, making them easier to find.
- **The product↔event link is now shared.** It was extracted into a new *event_product* module (installed automatically with event_sale). It can be reused by other apps, for instance selling event registrations from Point of Sale without installing the full Sales suite.
- The general 18.0 product-form rework applies here too: the Type field is a simple radio choice (Goods / Service), tracking is handled by the Tracked field, and the technical `detailed_type` field disappears from the UI and exports.
- Everything else announced in the 18.0 Sales/Events release notes (commission plans, combo products, Shopee/Amazon connectors, event visibility options, pop-up registration, retroactive leads) belongs to other modules and is **not** part of event_sale.

## Technical data model changes

No longer defined by event_sale (now provided by event_product / product):

- `product.template`: `detailed_type` selection extension `('event', 'Event Ticket')` removed, with its onchange and type mapping. The `service_tracking` extension `('event', 'Event Registration')` moved to the new module.
- `product.product`: `event_ticket_ids` one2many moved out; files were renamed (`product.py` -> `product_product.py` / `product_template.py`).
- `event.type.ticket`: removed `description`, `product_id`, `currency_id`, `price`, `price_reduce`, plus the related compute methods, `_default_product_id`, `_get_event_ticket_fields_whitelist` and `_init_column`.
- `event.event.ticket`: removed `price_reduce_taxinc`, `price_incl` and their compute methods, and `_compute_sale_available`.
- `event.event`: related `currency_id` removed.

Still inside event_sale:

- The ticket's product domain is now `[('service_tracking', '=', 'event')]` instead of `detailed_type = 'event'`.
- Sale order logic (partner propagation to registrations, catalog domain, confirmation check for lines missing an event or ticket) now tests `service_tracking == 'event'` on order lines.
- New methods: `sale.order._notify_get_recipients_groups`, `sale.order.line._check_event_registration_ticket`, `event.registration._get_event_registration_ids_from_order`, `product.template._prepare_service_tracking_tooltip`.
- Method signature delta: 0 modified, 4 added, 10 removed.

## How your habits should change

- Stop looking for the "Event Ticket" type. Create and maintain ticket products as **Service** with **Service Tracking = Event Registration**.
- On upgrade, existing Event Ticket products are converted to the Service type; their invoicing policy (invoice on ordered quantities) is kept.
- Any saved filter, automation, pricelist rule or custom report based on the product type "Event Ticket" (or on `detailed_type`) must be rewritten to use Service Tracking = Event Registration.
- Anything you built on the fields that moved (ticket price, product on a ticket, tax-included price, availability for sale, ticket list on the product) now relies on the *event_product* module: keep it installed in every database using event_sale.

## What you gain by migrating

- A single, simpler product model: no confusing duplicate type for event tickets, one Service type with an explicit Service Tracking value.
- Easier, more reliable filtering and reporting on event-related services.
- A shared product-event layer reused by other apps (e.g. selling events from Point of Sale), with fewer modules to install.
- A better customer experience: ticket links sent with the order confirmation.
- Clearer validation before confirming orders whose event lines are incomplete.
