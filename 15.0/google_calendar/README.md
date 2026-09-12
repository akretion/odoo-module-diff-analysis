# google_calendar migration guide (14.0 -> 15.0)

This guide covers the Odoo Community addon `google_calendar` (Google Calendar connector) between 14.0 and 15.0. It is written for functional users and for a customer still running 14.0.

## What's new for users

The official Odoo 15 release notes say exactly one thing about this addon:

> **Google Synchronization — Improved synchronization of recurring events.**

That sentence is precisely what this release delivers, and it matches the code changes described below. In day-to-day use you should now see:

- **Less duplication.** An event belonging to a recurring series can no longer be created twice, once in Odoo and once in Google.
- **Consistent series**, including the base event (the first occurrence of a recurrence).
- **Whole-series deletions** done in Google Calendar are correctly reflected in Odoo.
- **Date/time changes of a series** made in Google Calendar are now taken into account in Odoo instead of being silently ignored.
- **Attendee answers** (accepted / declined) are pushed from Odoo to Google, not only pulled from Google.
- **Fewer errors** on public calendars and on events whose organizer is not an Odoo user.

The other bullets of the 15.0 “Calendar” release-notes section (reminders when appointments are booked, scheduling slots from opening hours, custom sharing links, appointment types) belong to the **Appointments** application and are largely **Enterprise-only**: they are not part of this Community addon and are intentionally not listed here. The “Outlook Connector” item concerns another addon.

## Technical data model changes

**Field removed / default changed — `need_sync`**

In 14.0 the `calendar.recurrence` model redefined `need_sync = fields.Boolean(default=False)` to exclude recurrences from synchronization by default. This override is removed in 15.0, so the field value comes from the shared `google.calendar.sync` behaviour again. The column still exists (it is inherited), no destructive schema change is required: only the recurrence-specific default disappears.

**Other field / behaviour changes**

- `calendar.event.google_id` is now computed from the recurrence **only when the event has none** (`if not event.google_id and google_recurrence_id`), instead of overwriting an identifier that already exists.
- `calendar.event.create()` forces `need_sync = False` when `recurrence_id` **or** `recurrency` is set (previously only `recurrency`).
- `calendar.event.write()` now flags the parent recurrence with `need_sync = True` when an edit with `recurrence_update = 'all_events'` touches a Google-synced field.
- `calendar.attendee.write()` triggers a push to Google whenever `state` changes.
- `calendar.event._google_values()`: when the event has no Odoo responsible user, extended properties are written in the `private` namespace and only a minimal payload (`id`, `attendees`, `start`, `end`, `reminders`) is sent, avoiding Google “403” access errors on shared/public calendars.
- Recurrence rules are normalized before being sent: `UNTIL` is forced to UTC with a trailing `Z`.
- Deleting a record already removed locally no longer raises an error (`self.exists()`).

**New hooks (no existing method signature was changed)**

- `google.calendar.sync._write_from_google(gevent, vals)` and `_create_from_google(gevents, vals_list)` — generic entry points now used by the synchronization engine.
- `calendar.recurrence._write_from_google()`, `_create_from_google()`, `_cancel()` — the recurrence-specific logic moved into these hooks.

## How your habits should change

- Editing a recurring event in Google is now safe: time changes recreate the series in Odoo, other field changes update existing occurrences.
- Deleting a recurring series in Google propagates to Odoo; deleting it in Odoo propagates to Google.
- Accepting or declining an invitation in Odoo updates your status in Google.
- If you manage public calendars where the organizer is not an Odoo user, expect read-only-like behaviour for those events: Odoo now avoids pushing data it has no right to change.
- No manual data migration or field mapping is needed for the removed `need_sync` override.

## What you gain by migrating

Migrating to 15.0 mainly buys you **reliability of the Google Calendar synchronization**, especially for recurring events — historically the weakest point of the connector. Fewer duplicates, no more ghost series, working series deletions and time changes, and attendee statuses that finally travel both ways. It also removes a class of hard errors (HTTP 403) on shared and public calendars.

Because these fixes are corrective rather than cosmetic, a 14.0 database upgraded to 15.0 does not need any specific data cleanup: the improvements apply as soon as the addon is updated.
