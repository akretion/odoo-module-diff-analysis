# google_calendar migration guide (17.0 -> 18.0)

## What's new for users

The official Odoo 18.0 release notes contain no dedicated section for Google Calendar synchronization. The only Calendar-related entry, “Default appointment duration”, concerns the Appointments feature, not this connector, so it is not presented here as a change of this addon.

The user-visible evolution therefore comes from the fixes shipped in 18.0:

- Recurrent events are now correctly updated on the Google side.
- Synchronization resumes properly after being paused or stopped.
- All-day recurring events are no longer duplicated.
- Events pushed to Google reuse the values already inserted instead of overwriting them.
- Time off events no longer carry a video call URL.
- The “connect your Google account” onboarding flow was fixed.

## Technical data model changes

**Model removed:** `google.calendar.credentials`, with its fields `user_ids`, `calendar_rtoken`, `calendar_token`, `calendar_token_validity`, `calendar_sync_token`, `calendar_cal_id`, `synchronization_stopped`. The SQL constraint `google_token_uniq` disappears with it.

**`res.users`:** `google_calendar_account_id` (Many2one) is removed. The related fields `google_calendar_rtoken`, `google_calendar_token`, `google_calendar_token_validity`, `google_calendar_sync_token`, `google_calendar_cal_id` and `google_synchronization_stopped` are kept but now point to `res.users.settings` and are restricted to `base.group_system` (read through `sudo()`). The `SELF_READABLE_FIELDS` / `SELF_WRITEABLE_FIELDS` overrides are gone.

**New fields on `res.users.settings`:** `google_calendar_rtoken`, `google_calendar_token`, `google_calendar_token_validity`, `google_calendar_sync_token`, `google_calendar_cal_id`, `google_synchronization_stopped` — all with `groups='base.group_system'`. New methods there: `_get_fields_blacklist` (keeps the secrets out of `session_info`), `_google_calendar_authenticated`, `_is_google_calendar_valid`, `_refresh_google_calendar_token`, `_set_google_auth_tokens`.

**Method signature deltas:**

- `google.calendar.sync._sync_google2odoo(google_events, write_dates=None, default_reminders=())`: new optional `write_dates` argument.
- New helpers on `google.calendar.sync`: `_check_any_records_to_sync`, `_get_post_sync_values`, `_handle_allday_recurrences_edge_case`, `_need_video_call`.
- New methods on `res.users`: `_check_pending_odoo_records`, `_has_setup_credentials`, `check_calendar_credentials`, `check_synchronization_status`.
- `calendar.attendee._send_mail_to_attendees` is removed and `calendar.event._skip_send_mail_status_update` is added, so no email is sent when only the synchronization status changes.

## How your habits should change

- Credentials and synchronization data now live on each user's settings record, not on the user record itself.
- Only administrators (System group) can see these values; a regular user can no longer read another user's token, and the access rule allowing it was dropped.
- Any customization that reads `user.google_calendar_account_id` or the related token fields directly must be reworked to use `res.users.settings` and `sudo()`.
- The visible migration code does not carry over the historical tokens stored in the removed model: after the upgrade, users should reconnect their Google account from their Calendar settings and restart the synchronization.
- No other daily habit changes: creating, editing and syncing events works as before, only more reliably.

## What you gain by migrating

- A cleaner, more secure data model: Google tokens are per-user settings, reserved to administrators and excluded from the session information sent to the browser.
- A credential storage that no longer loads the heavy `res.users` model and no longer blocks rows on read, which improves overall performance.
- Reliability fixes on the points that most often annoy users: recurring events, all-day duplicates, resuming synchronization, and pushed-event values.
- Less noise: no more status-update emails, and no video call link on time off events.
- A supported code base, aligned with the Odoo 18.0 maintenance and future upgrades, instead of staying on a version whose token model is being phased out.
