# google_calendar migration guide (17.0 -> 18.0)

## What's new for users

The Odoo 18.0 release notes contain **no new feature for the Google Calendar connector**. The only "Calendar" entry published for 18.0 concerns the default duration of an appointment, which belongs to the Appointments application and not to this addon. Nothing in the notes relates to Google synchronization, and no Enterprise-only feature should be read into this module.

Practically, the user experience is unchanged: same menus, same **Sync with Google**, stop and restart actions, same scheduled synchronization, same behavior before and after the upgrade. What changes is *where* the Google credentials are stored and *who* may read them — a technical and security change, detailed below.

## Technical data model changes

**Removed**
- The model `google.calendar.credentials` is deleted entirely, with its fields (`calendar_rtoken`, `calendar_token`, `calendar_token_validity`, `calendar_sync_token`, `calendar_cal_id`, `synchronization_stopped`, `user_ids`) and its helper methods.
- On `res.users`: the fields `google_calendar_account_id` (Many2one to the removed model) and the SQL constraint `google_token_uniq` are gone, as are the `SELF_READABLE_FIELDS` / `SELF_WRITEABLE_FIELDS` entries for `google_synchronization_stopped` and `google_calendar_account_id`.

**Added / moved**
- `res.users.settings` is now extended with the synchronization data: `google_calendar_rtoken`, `google_calendar_token`, `google_calendar_token_validity`, `google_calendar_sync_token`, `google_calendar_cal_id` and `google_synchronization_stopped`. Each of these fields carries `groups='base.group_system'`, so only System administrators may read or write them.
- A new `_get_fields_blacklist()` keeps those fields out of `session_info`, so tokens are never pushed to the web client.

**Changed behavior**
- `res.users` keeps the same field names for backward compatibility, but they are now *related* fields pointing to `res.users.settings`, and **all of them** now carry `groups='base.group_system'`. In 17.0 only the refresh token was restricted; the access token, its validity, the sync token and the calendar ID were readable by ordinary users.
- The previous rule allowing a non-admin to read another user's token was deliberately dropped.
- Internal code now accesses these values through `sudo()`: `_get_google_calendar_token`, `_get_google_sync_status`, `_sync_all_google_calendar`, `_sync_google_calendar`, `_is_google_calendar_valid`, `stop_google_synchronization` and `restart_google_synchronization`.

**Method signature deltas**
- `_set_auth_tokens` becomes `_set_google_auth_tokens` on `res.users.settings`.
- `_google_calendar_authenticated`, `_is_google_calendar_valid` and `_refresh_google_calendar_token` move from `google.calendar.credentials` to `res.users.settings`.
- `restart_google_synchronization()` no longer creates a credentials record; it simply resets the stopped flag and restarts synchronization for events and recurrences.

## How your habits should change

- **Administrators only:** token and synchronization fields are now reserved to System administrators. A regular user can no longer inspect their own token, and no user can read another user's. Use an administrator account for support or diagnostics.
- **No more credential record per user:** you will no longer see a `google.calendar.credentials` record behind each user. There is nothing to create, link or clean up manually; the settings follow the user automatically.
- **Stop / restart still available**, but writes are performed with elevated rights. Restarting a synchronization no longer depends on a credentials entry existing.
- **Customizations must be reviewed:** any Studio field, report, server action, import/export template or integration referencing `google_calendar_account_id` or the `google.calendar.credentials` model will break and must be adapted.
- **After the upgrade, check each user's connection** (Settings / user preferences) and re-authorize Google if a calendar no longer synchronizes.

## What you gain by migrating

- **Stronger protection of OAuth tokens.** Refresh tokens, access tokens and sync tokens are restricted to System administrators and removed from the browser session payload, reducing exposure — a real audit argument.
- **A lighter, healthier `res.users` table.** Credentials no longer sit on a heavily-read model, which reduces row locking and contention when many users are logged in.
- **A single, coherent place for user settings.** Synchronization data now lives with the other per-user preferences in `res.users.settings`, making future additions cleaner and upgrades easier.
