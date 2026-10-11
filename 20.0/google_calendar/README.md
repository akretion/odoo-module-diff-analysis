# google_calendar migration guide (19.0 -> 20.0)

This addon is the Community connector between Odoo Calendar and Google Calendar. This guide summarizes what changes for functional users and administrators moving from 19.0 to 20.0.

## What's new for users

- **Sync multiple Google calendars.** Odoo no longer synchronizes only your Google primary calendar: several Google calendars can now be synced with Odoo, with changes reflected both ways.
- **Multiple calendars in one place.** You can create and manage several calendars in Odoo, including shared team calendars, and pick them in the calendar sidebar filters.
- **Google Calendar synchronization is significantly faster and more reliable** than in 19.0.

Note: other Calendar items of the 20.0 release notes (Outlook synchronization, booking pages, mobile redesign, "Meet with", meeting-title time detection, work locations) do not belong to this Community addon and are out of scope here.

## Technical data model changes

- `calendar.calendar` now inherits `google.sync` and gains: `google_sync_token` (related), `google_sync_enabled` (related, writable), and `is_import_pending` — the "Needs Import" flag set on calendars discovered in Google and not yet enabled for import.
- `calendar.user` gains new `access_role` values `reader` and `freeBusyReader` (cascade on module removal), plus `google_sync_enabled` and `google_sync_token`.
- **Removed:** the single `google_calendar_cal_id` on user Google settings and its related field on `res.users`. It is replaced by a per-calendar `google_id` and a per calendar/user sync token, which is what enables multi-calendar sync.
- `calendar_id` is now a synchronized field of `calendar.event`; `calendar.recurrence` also stores `calendar_id`.
- Models were split: `calendar.py` becomes `calendar_calendar.py`, `calendar_event.py` and `calendar_user.py`; abstract `google.calendar.sync` becomes the generic `google.sync` plus the event-specific `google.event.sync` (the `active` field moved to `google.event.sync`).
- Changed method signatures: `_odoo_values(google_event, calendar, default_reminders=())`, `_create_from_google(gevents, calendar, vals_list)`, `_write_from_google(gevent, vals, calendar)`, `_google_delete(service, path, google_id)`. `_is_google_insertion_blocked(sender_user)` is replaced by `_should_be_synced()` and `_get_event_owner()`.
- Outgoing selection now also covers events of your writable calendars (`calendar_id in writable_calendar_ids`); incoming events skip calendars flagged `is_import_pending` (`_get_calendar_id_domain`).
- Other behaviour changes: Odoo "members only" privacy maps to Google "private"; read-only calendars force `user_can_edit = False`; archiving an event is always permitted.

## How your habits should change

- **Enabling sync is now per calendar.** After the first sync, your Google calendars appear with a "needs import" status, hidden in the filters, and no events are imported until you explicitly enable sync for each of them (user preferences or the calendar list view). You get a notification with a link to that list.
- Calendar filters become essential: events are now shown based on their calendar rather than on the current user being an attendee, so shared, read-only and holiday calendars behave correctly.
- Events without attendees are no longer given a fake attendee (you); they stay as they are in Google.
- **Deleting a synced calendar in Odoo no longer deletes it in Google.** It reverts to a pending calendar that can be imported again, protecting your Google data. Delete it in Google if that is really what you want.
- Renaming a calendar in Odoo updates the Google summary; a rename done in Google may not be picked up immediately.
- If the same Google account is used by several Odoo users, a calendar keeps a single owner; the others are demoted to writers.

## What you gain by migrating

- Genuine multi-calendar synchronization (primary, secondary, shared team, read-only and resource calendars) instead of a single primary-calendar flow.
- Incremental, per-calendar sync tokens: faster syncs, fewer full re-imports, less API traffic.
- Safeguards on data: no destructive deletion of Google calendars, no invented attendees, no events wrongly attributed to the wrong owner in shared calendars.
- Clear, enforced permissions coming from Google ACL roles (reader, free/busy reader), so read-only calendars can be imported without being silently editable.
- Predictable conflict handling: for calendars Odoo is authoritative, for events the most recently updated side wins.
