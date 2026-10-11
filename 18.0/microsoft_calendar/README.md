# microsoft_calendar migration guide (17.0 -> 18.0)

## What's new for users
The official Odoo 18.0 release notes contain **no new feature for the Microsoft Calendar synchronization addon**. The "Calendar" section only mentions the default duration of an appointment (Appointments app), which is unrelated to Outlook / Microsoft 365 sync. The Knowledge, Time Off, Planning and Accounting sections do not concern this addon either.

In practice, the Outlook ↔ Odoo synchronization behaves exactly as in 17.0: same connection flow, same stop/restart synchronization actions, same events and recurrences synced. What changes is *where* the synchronization data is stored and *who* can see it:

- Microsoft sync credentials, sync token, "synchronization stopped" flag and last sync date are now reserved to users in the **System Administrator** group.
- The possibility for a normal (non-admin) user to read another user's sync token has been dropped.

## Technical data model changes
Two refactors happened between 17.0 and 18.0.

**1. Microsoft identifiers split into two real fields (PR #140076)**
- 17.0 stored both the Outlook organizer event id and the universal id (iCalUId) inside a single `microsoft_id` Char, glued with a `:` separator. `ms_organizer_event_id` and `ms_universal_event_id` were non-stored computed fields with inverse and search methods.
- 18.0: `microsoft_id` is now "Organizer event Id" (stored, indexed, `copy=False`), and `ms_universal_event_id` is a new stored indexed field "Universal event Id" (`copy=False`). `ms_organizer_event_id` **no longer exists** — use `microsoft_id` instead.
- `calendar.recurrence.rule` loses its own `microsoft_id` ("Microsoft Calendar Recurrence Id"); recurrence rules now use the generic field from `microsoft.calendar.sync`.
- Removed helper methods `_compute_organizer_event_id`, `_compute_universal_event_id`, `_set_event_id`, `_search_event_id`, `_search_organizer_event_id`, `_search_universal_event_id`, and the `event_id_storage` helper (`combine_ids` / `split_ids` / `IDS_SEPARATOR`).
- Deleting or cancelling an event now clears **both** ids.

**2. Sync credentials moved to `res.users.settings` (PR #151537)**
- Model `microsoft.calendar.credentials` ("Microsoft Calendar Account Data") and the field `res.users.microsoft_calendar_account_id` (with its unique SQL constraint) were **removed**.
- New fields on `res.users.settings`: `microsoft_calendar_sync_token`, `microsoft_synchronization_stopped`, `microsoft_last_sync_date` — all restricted to `base.group_system`, `copy=False`.
- On `res.users`, `microsoft_calendar_sync_token`, `microsoft_synchronization_stopped` and `microsoft_last_sync_date` are now related to `res_users_settings_id` and are also restricted to `base.group_system`. Internal code reads and writes them with `sudo()`.
- New `_get_fields_blacklist()` in `res.users.settings` keeps these fields out of `session_info`.
- `_check_microsoft_calendar_credentials()` was removed.

## How your habits should change
- **Saved filters, exports, automated actions:** anything using `ms_organizer_event_id` must be rewritten to `microsoft_id`. Filters on `ms_universal_event_id` still work and are now much faster, because the field is stored.
- **Custom code / reports / imports:** any reference to `microsoft.calendar.credentials` or `microsoft_calendar_account_id` must be re-pointed to the corresponding `res.users.settings` fields.
- **Non-admin users:** sync token, "synchronization stopped" and last sync date are no longer readable by them. If a dashboard or a view displayed them, restrict access to System Administrators.
- **Technical maintenance:** reading or writing tokens must be done as an administrator (or through `sudo()`), not by a regular user.
- **Day-to-day use is unchanged:** connect your Outlook account, pause/restart the synchronization, and let the cron job run exactly as before.

## What you gain by migrating
- **Cleaner and faster identifiers.** Both Microsoft event ids are plain stored, indexed fields. Searching, grouping and filtering on them is significantly faster than the 17.0 computed/inverse/search workaround, and there is no more parsing of a `':`-separated string.
- **More reliable synchronization data.** Ids are written and cleared explicitly (create, delete, cancellation, recurrence recomputation), reducing the risk of stale or ambiguous ids.
- **Stronger security.** Tokens and synchronization state are system-only fields, and the cross-user token read rule is gone.
- **Lighter `res.users` model.** Credentials no longer sit on `res.users`, avoiding row-locking issues on reads and making the model easier to maintain.
- **A future-proof base.** No deprecated fields left over to migrate later, which keeps future upgrades and custom developments simpler.
