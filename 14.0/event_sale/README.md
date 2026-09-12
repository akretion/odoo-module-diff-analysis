# event_sale migration guide (13.0 -> 14.0)

## What's new for users

**Ticketing no longer requires Sales or eCommerce.** In 14.0 tickets are managed directly by the Events app: you can create multi-ticket events and issue tickets while handling payments outside Odoo (bank transfer, cash, etc.). The `event_sale` addon is now only needed for the online selling flow (quotations, orders, invoices, registrations linked to a sale order) and it keeps products, prices and sales analysis.

**Ticket templates.** Tickets are configured on the Event Type as lightweight templates and copied onto the event when the type is applied, instead of one shared ticket record serving both.

**UTM reporting on attendees.** Campaign / Source / Medium can be tracked on registrations, taken from the related sale order when there is one, and can also be set directly for events with no sale order.

**Sales integration.** Easier navigation between ticket sale orders, events and attendees.

Note: most other 14.0 Events release-note items (Exhibitors, Tracks, YouTube Live, Rooms, Sponsors, Registration Desk, etc.) relate to the Events app's own online-event features or to the Enterprise edition; they are not part of `event_sale`.

## Technical data model changes

Moved from `event_sale` to the Events app:

- `event.event.ticket` is no longer defined in `event_sale`; the model (seats, sale dates, availability, registrations) now lives in Events.
- New lightweight `event.type.ticket` model for template tickets, split from `event.event.ticket` (previously one table where `event_id` / `event_type_id` behaved differently).
- `event.type` no longer inherits ticketing: `use_ticketing` and `event_ticket_ids` were removed from `event_sale`.

Removed / relocated fields:

- `event.event`: `event_ticket_ids` (now inherited from Events), computed `start_sale_date`, overrides `_onchange_type` and `_compute_event_registrations_open`.
- `event.registration`: `event_ticket_id`, `_onchange_event_id` and the `_check_ticket_seats_limit` constraint moved to Events.
- Kept in `event_sale`: `sale_order_lines_ids`, event currency/amount, and on tickets `product_id`, `price`, `price_reduce`, `price_reduce_taxinc`.

Changed behaviours:

- UTM on registrations: `campaign_id` / `source_id` / `medium_id` (related to the sale order, stored) become `utm_campaign_id` / `utm_source_id` / `utm_medium_id`, computed from the sale order, stored, editable and copyable.
- Method renames: `_get_ticket_multiline_description_sale()` -> `_get_ticket_multiline_description()`; `_get_price_reduce_tax()` -> `_compute_price_reduce_taxinc()`.
- New hook `_get_event_ticket_fields_whitelist()` on `event.type.ticket` controls which fields (product, price) are copied from a template to an event ticket.
- Migration support: existing template tickets get `product_id` filled with the generic registration product.

## How your habits should change

- Configure tickets on the event, and template tickets on the event type — not from the Sales side. Sales still provides the product and the price.
- You can run ticketing without installing Sales/eCommerce; if you do sell online, your quotation/order flow is unchanged.
- UTM fields on registrations are now regular stored `utm_*` fields; filters, reports and automations built on the old `campaign_id` / `source_id` / `medium_id` must be re-pointed.
- For events paid outside Odoo, report on registrations rather than on ticket sale orders.

## What you gain by migrating

- Ticketing available to every event organiser, not only those selling online through Odoo.
- A cleaner model: template tickets versus event tickets, with no more "ticket belongs either to a type or to an event".
- Reliable UTM statistics on attendees, even without any Sale Order.
- Sales integration preserved: products, prices, sales analysis, and quick navigation from events to orders and attendees.
