# event migration guide (13.0 -> 14.0)

## What's new for users

The main user-facing change is ticketing: in 14.0, basic tickets can be managed directly in the Events app without installing Sales or eCommerce. You can define ticket types on an event category or directly on an event, and registrations can be linked to a chosen ticket. Sales and eCommerce remain useful only when you want online orders, payment and sales analysis.

Other relevant release notes items:
- Event Tags can be used to create custom website filters.
- The event views were refreshed: Kanban cards were redesigned, custom stages are used to manage events, and the list view was updated.
- The "Minimum Seats" field was removed.

The 14.0 release notes also mention many features from the wider Events suite: online tracks, YouTube Live, Exhibitors, virtual Rooms, Gamification, Magic Buttons, the Registration Desk, improved mobile/PWA attendee views, CRM and Sales integrations. Those are not part of this Community `event` addon change set, and some belong to Enterprise or other apps. Do not treat them as automatically included in a Community migration.

## Technical data model changes

Based only on the addon diff:

- New ticketing models:
  - `event.type.ticket` (template tickets on an event category).
  - `event.event.ticket` now lives in `event` (previously tied to `event_sale`), with `registration_ids`, computed seats (`seats_reserved`, `seats_available`, `seats_unconfirmed`, `seats_used`), and a required `event_id`.
- `event.type` additions: `use_ticket`, `event_type_ticket_ids`.
- `event.type` removals: `use_hashtag`, `default_hashtag`.
- `event.type` behavior: `default_registration_max` and `event_type_mail_ids` are now stored, editable computed fields. `event_type_mail_ids` is reset when `use_mail_schedule` is unchecked.
- `event.event` additions: `event_ticket_ids`, `start_sale_date` (computed from tickets).
- `event.event` removal: `twitter_hashtag`.
- `event.event` changes:
  - `seats_availability` selection (`limited`/`unlimited`) replaced by the Boolean `seats_limited`.
  - `date_tz`, `seats_max`, `auto_confirm`, `is_online`, `event_mail_ids`, `event_ticket_ids` and `address_id` became stored, editable computed fields driven mainly by `event_type_id`.
  - `country_id` is still related to the venue, stored and copyable.
  - `event_type_id` uses `ondelete='set null'`.
- `event.registration` changes:
  - Added `event_ticket_id`.
  - `name`, `email`, `phone`, `mobile` are computed from the contact and remain editable.
  - `date_closed` is computed when the registration is marked attended.
  - New constraints enforce event/ticket coherence and ticket seat limits.
- `event.event.ticket` / `event.type.ticket`: `seats_availability` replaced by `seats_limited`; `seats_limited` is computed from `seats_max`.
- No tracked public method signature deltas were reported; the impact is mostly field behaviour and ORM computation.

## How your habits should change

- You no longer need Sales or eCommerce just to create tickets. Configure tickets on the event category or event. If you sell online, keep using the Sales/eCommerce integration for products, prices and orders.
- Instead of choosing "Unlimited" or "Limited" seats, tick the "Maximum Attendees" checkbox and enter a maximum. Seat limits can also be set per ticket.
- Ticket selection is now part of registration. Make sure the selected ticket belongs to the event; Odoo blocks inconsistent choices.
- Event category defaults are applied through stored computed fields. Changing the category updates configuration such as seats, confirmation, timezone, mail schedule and tickets, while keeping values editable afterwards.
- If you disable automatic emails on an event category, its mail schedule is cleared. This matches the ticket template behaviour.
- The Twitter hashtag fields on events and event categories are gone. Use the social marketing and website tools instead.
- The "Minimum Seats" field is removed.
- Contact details and the attended date on registrations are now filled automatically from existing data; you can still override them.

## What you gain by migrating

- Ticketing is available in Community Events without Sales or eCommerce, lowering the cost and complexity of managing paid or free ticket types.
- Event categories can carry reusable ticket templates, making recurring events faster to configure.
- Per-ticket seat tracking is more precise, with reserved, available, unconfirmed and used seats.
- Clearer and more reliable data: computed stored fields replace fragile onchange/default logic, and new constraints prevent invalid event/ticket combinations.
- Event tags help organise events and build website filters.
