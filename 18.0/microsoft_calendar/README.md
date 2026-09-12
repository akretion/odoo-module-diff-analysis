# microsoft_calendar migration guide (17.0 -> 18.0)

## What's new for users

The official 18.0 release notes contain **no functional item for the Microsoft/Outlook Calendar connector**. The only "Calendar" note of the release ("default appointment duration") concerns the Appointments feature, not Outlook synchronisation, and is not part of this addon. `microsoft_calendar` is a technical integration addon: its 18.0 changes are delivered as bug fixes and refactoring.

Concretely, on 18.0 the connector behaves better in these situations:

* Creating or updating an event **on behalf of another organizer** works reliably, and the event is properly recreated when the organizer changes.
* **External/portal users** no longer hit access (ACL) errors when events are created on their calendar.
* **Attendee confirmation statuses** coming from Outlook are correctly applied.
* **Deleting a recurring event from the list view** no longer leaves incorrect or orphaned records.
* **Old events are no longer re-pushed** over and over during synchronisation (sync "spam" fixed).
* Event links / online-meeting URLs added to the Microsoft event are improved.
* Credential checks, onboarding and the **"synchronise now" button** correctly report sync status.

## Technical data model changes

### Microsoft IDs split into two stored fields
* On the sync mixin (`microsoft.calendar.sync`, inherited by `calendar.event` and `calendar.recurrence`):
  * `microsoft_id` — "Organizer event Id", now stored and indexed.
  * `ms_universal_event_id` — "Universal event Id" (Outlook iCalUId), now stored and indexed.
* In 17.0 `microsoft_id` was a workaround storing both ids concatenated with a `:` separator, while `ms_organizer_event_id` / `ms_universal_event_id` were computed fields with inverse and custom search methods.
* **Removed:** field `ms_organizer_event_id` (use `microsoft_id`), the field `microsoft_id` on `calendar.recurrence.rule`, and the methods `_compute_organizer_event_id`, `_compute_universal_event_id`, `_set_event_id`, `_search_event_id`, `_search_organizer_event_id`, `_search_universal_event_id`.
* Any custom filter, import/export, report or automated action using `ms_organizer_event_id`, or expecting a `:`-joined `microsoft_id`, must be updated.

### Credentials moved from `res.users` to `res.users.settings`
* **Removed model** `microsoft.calendar.credentials`.
* **Removed on `res.users`:** `microsoft_calendar_account_id`, plus the `SELF_READABLE_FIELDS` / `SELF_WRITEABLE_FIELDS` extensions, and `_check_microsoft_calendar_credentials`.
* **Added on `res.users.settings`:** `microsoft_calendar_sync_token`, `microsoft_synchronization_stopped`, `microsoft_last_sync_date`, all restricted to `base.group_system` and read through `sudo()`; plus `_get_fields_blacklist`.
* The rule allowing a non-admin user to read another user's token was **dropped**: tokens are now reserved to system administrators.
* `_refresh_microsoft_calendar_token` now takes a `service` argument; new helper `_has_setup_microsoft_credentials`.

### Other signature changes (internal API)
* `calendar.event`: `_check_organizer_validation`, `_get_organizer_user_change_info`, `_recreate_event_different_organizer`, `_get_event_user_m`, `_skip_send_mail_status_update`, `_update_attendee_status`, `_need_video_call`, `unlink`.
* `calendar.recurrence`: `_get_event_user_m`.
* `microsoft.calendar.sync`: `_check_old_event_update_required` added; `_impersonate_user` removed.
* `calendar.attendee`: `_send_mail_to_attendees(mail_template, force_send=False)` changed.
* `res.users`: `check_calendar_credentials`, `check_synchronization_status` added.

## How your habits should change

* Day-to-day synchronisation looks the same: connect your Outlook account, events flow both ways.
* If you are **not a system administrator**, you can no longer read another user's synchronisation token. Scripts and server actions reading tokens must now run as an administrator.
* Credentials are no longer visible as a separate "Microsoft Calendar Credentials" record; they live in the user's settings.
* Re-check any saved filter, export, import template or automated action built on `ms_organizer_event_id` or on a `:`-joined `microsoft_id`: point them to `microsoft_id` / `ms_universal_event_id`.
* Custom developments calling `_impersonate_user` or `_check_microsoft_calendar_credentials` must be rewritten.

## What you gain by migrating

A more robust and more secure Outlook connector: fewer failed event creations (delegated organizers, external users), correct attendee statuses, cleaner recurring-event deletion, no more duplicated/re-pushed old events, and improved credential checks. Tokens are also better isolated (system-group only), which reduces the risk of leaking calendar access data.
