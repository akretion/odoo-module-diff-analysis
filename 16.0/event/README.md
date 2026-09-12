# event migration guide (15.0 -> 16.0)

## What's new for users

The official 16.0 release notes for the Events app mention:

- **Location ordering**: choose the order in which event locations are listed.
- **Mandatory questions**: require attendees to answer questions when registering.
- **Registration statistics**: a dedicated stat button to track an event's registrations.
- **Tag ordering**: sort event tags as you want on your website.
- **Website theme**: event pages now adapt to the website theme.
- **Timezones**: times are shown with their timezone on every page of an event, on the website.

Only these items are relevant to this addon; the release notes mix Community and Enterprise features, and the rest of their Events/Website sections is unrelated. The most important 16.0 change for `event` is not described there: it is a performance and robustness rework of seat management (see below).

## Technical data model changes

**Seat counters are no longer stored.** On `event.event` and `event.ticket`, `seats_reserved`, `seats_available`, `seats_unconfirmed` and `seats_used` moved from `store=True` to `store=False`. `seats_expected` (events) is now computed by `_compute_seats`, replacing the removed `_compute_seats_expected`.

**Other field-level changes**
- `event.type.seats_max` compute renamed `_compute_default_registration` → `_compute_seats_max`.
- New `event.ticket` fields: `is_launched` (computed boolean) and `is_sold_out` (computed boolean). `sale_available` now derives from them.
- The `is_launched()` method on `event.ticket` was removed; use the field instead.
- `_compute_event_registrations_open` now depends on `seats_max` and treats `seats_max = 0` as unlimited.
- `_compute_event_registrations_sold_out` was rewritten around ticket `is_sold_out`.

**Constraints**
- `event.event._check_seats_limit` → `_check_seats_availability(minimal_availability=0)`.
- `event.ticket._constrains_seats_available` → `_check_seats_availability(...)`.
- `event.registration._check_seats_limit` and `_check_ticket_seats_limit` were removed; seat checks now run from `write()` on state changes and from `toggle_active()`.
- `_check_auto_confirmation` now only checks the event's `auto_confirm` flag.
- The date-coherency error on tickets now names the offending ticket.

**Display names**: `name_get()` on events and tickets supports the context key `name_with_seats_availability`, appending "(Sold out)" or "(N seats remaining)".

**Removed**: the Reporting/Event analysis that depended on stored seat values.

## How your habits should change

- Seat counters (reserved, available, unconfirmed, used, expected) are computed on the fly. They are still visible on forms and lists, but they can no longer be used to group, filter or aggregate in stored/custom reports, and the Event reporting menu is gone. Use the registration list and standard reports instead.
- Because the values are no longer stored, availability is always up to date, and search filters on seat fields were rewritten so that they keep working.
- All events and tickets are now shown in backend form views with their seat availability in the name, e.g. "Training (12 seats remaining)".
- Seat validation is triggered when registrations are confirmed, cancelled or archived, not by the registration constraints themselves. Error messages now list each event/ticket with the number of missing seats, e.g. *"There are not enough seats available for: - "Gala": Missing 3 seats."*
- The event configurator wizard validates event/ticket consistency when it is closed.
- In the Registration Editor wizard, a warning tells you when free registrations were not confirmed because of insufficient seat availability; button wording was clarified.
- Automatic confirmation now depends only on the event's Auto-confirmation setting.

## What you gain by migrating

- **Fewer errors under load**: unstored seat computations remove the flood of concurrent updates that caused "concurrent update" errors when many people registered at the same time for a popular event.
- **Better performance**: far fewer write queries are issued for seat recomputation.
- **Clearer feedback**: sold-out and remaining-seat information appears directly in event and ticket names, and error messages state exactly how many seats are missing, and for which event or ticket.
- **Safer registration flow**: the wizards warn you upfront when registrations cannot be confirmed for lack of seats.
- **Website improvements** for event pages: timezone display, theme adaptation, location ordering, mandatory questions, tag ordering.
