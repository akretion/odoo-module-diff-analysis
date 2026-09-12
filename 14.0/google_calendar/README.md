# google_calendar migration guide (13.0 -> 14.0)

The Google Calendar connector was refactored in 14.0 (task 2126717, PR #42031) to follow the calendar application refactoring and to use two new Google APIs: incremental sync and extended properties (event metadata).

## What's new for users

Official 14.0 release notes are sparse for this addon. Under "Calendar", the only item that concerns google_calendar is:

- **Recurrence**: when you modify an event that is part of a series, Odoo now asks what to change — this event only, this and all future events, or every event of the series. Google synchronization follows the same logic: the recurrence is synchronized as one object, and modified/excluded occurrences are handled explicitly.

The other Calendar release-note item, "Outlook Synchronization", belongs to a separate connector, not to this addon.

## Technical data model changes

Removed:
- `calendar.event.oe_update_date` ("Odoo Update Date")
- `calendar.attendee.google_internal_event_id` and `calendar.attendee.oe_synchro_date`, plus their SQL unique constraint
- `res.users.google_calendar_last_sync_date` ("Last synchro date")
- `res.config.settings.server_uri`
- the `google.calendar` AbstractModel (models/google_calendar.py, ~967 lines), replaced by `models/google_sync.py`, `models/calendar_recurrence_rule.py` and helpers in `utils/`

Added:
- Abstract model `google.calendar.sync` (`google_id`, `need_sync`, `active`), the shared sync base
- `calendar.event.google_id` (computed from the recurrence, stored, editable): the Google event id now lives on the event, not on each attendee
- `calendar.recurrence` inherits the sync mixin: recurrences have their own `google_id` and are synchronized as such
- `res.users.google_calendar_sync_token` ("Next Sync Token")
- `res.users.google_calendar_rtoken` / `google_calendar_token` are now restricted to `base.group_system`

Changed behaviour:
- Synchronization is incremental (sync token) instead of date based; a full sync happens when no token exists or the token is rejected
- Odoo record id and the "real" owner are stored in Google events as extended properties metadata
- A record synchronized with Google can no longer be deleted — it must be archived (a `UserError` is raised); archiving cancels the Google event
- `create()` now uses `@api.model_create_multi`, and the `recurrence_update` key ('all_events' / 'future_events') drives which occurrences are re-synced
- Old helpers (`get_fields_need_update_google`, `synchronize_events`, `synchronize_events_cron`) are replaced by `_get_google_synced_fields`, `_get_records_to_sync`, `_sync_google2odoo`, `_sync_odoo2google` and the `_sync_all_google_calendar` cron

## How your habits should change

- Deleting a Google-synced event is refused: archive it instead, so the deletion can still be propagated to Google.
- The first synchronization after the migration is a full one and may take longer; later ones are incremental and fast.
- When editing a recurring meeting, answer the scope question (this event / this and future / all): it decides what is pushed to Google.
- Google tokens are now visible only to administrators (system group).

## What you gain by migrating

- Faster, lighter synchronization using Google's incremental sync token.
- Cleaner conflict handling: last update wins, and ids stored as Google extended properties avoid duplicates and reconcile owners.
- Recurring meetings are synchronized properly, including modified or excluded occurrences.
- No silent data loss: synchronized events are archived instead of being hard-deleted.
- A cleaner, documented sync architecture, easier to maintain and shared with the next-generation connectors.
