# lunch migration guide (19.0 -> 20.0)

## What's new for users

The Lunch module no longer asks you whether a time is AM or PM. Every time used to
schedule automatic actions is now a plain 24-hour time, which removes the ambiguity
that came with the old 12-hour + AM/PM combination.

Nothing else in the daily ordering flow (suppliers, products, orders, alerts) was
changed by this release: the rework is limited to how scheduled times are entered,
stored and displayed.

Note: no official 19.0 -> 20.0 release note extract covers the `lunch` addon, so this
guide is based on the actual code changes shipped in the module.

## Technical data model changes

Two selection fields disappeared:

- `lunch.alert.notification_moment` (Selection: `am` / `pm`, default `am`, required) — removed
- `lunch.supplier.moment` (Selection: `am` / `pm`, default `am`, required) — removed

The time fields themselves are kept, but their meaning was widened:

- `lunch.alert.notification_time` (Float, default 10.0) now covers the whole day
- `lunch.supplier.automatic_email_time` (Float) now covers the whole day

The SQL constraints that guard them were updated accordingly, including their user
messages:

- `notification_time` must now be between 0 and 24 (was 0 to 12)
- `automatic_email_time` must now be between 0 and 24 (was 0 to 12)

Internal helper changes (no impact on your data, but relevant for customizations):

- `float_to_time` was removed from `lunch_supplier` and is now imported from
  `odoo.tools.date_utils`; its signature lost the second argument
  (`float_to_time(hours, moment='am')` -> `float_to_time(hours)`).
- `time_to_float` (local helper) was removed.
- The cron dependency sets `CRON_DEPENDS` in both `lunch.alert` and
  `lunch.supplier` no longer list `notification_moment` / `moment`.
- Cron scheduling (`_compute_*` on `sendat_tz`) now builds the send time from the
  float value alone, without applying an AM/PM offset.

A companion upgrade script (referenced as `odoo/upgrade#8892`) ships with this change
to translate existing AM/PM values into 24-hour times.

## How your habits should change

- Enter times as 24-hour values: `13.5` means 13:30, `18.75` means 18:45,
  `0.0` means midnight. The old "10.0 + PM = 22:00" logic is gone.
- There is no AM/PM toggle any more. If an alert or a supplier was configured with
  PM before the migration, verify the resulting time after the upgrade: what was
  meant as an afternoon slot can now read as an early-morning slot if it was not
  converted.
- The allowed range is wider: you can now schedule alerts and supplier emails at any
  hour up to 24, whereas 12 was previously the maximum.
- Updates that used to require touching two fields (time + AM/PM) are now a single
  edit, so cron rescheduling behaves more predictably.

Recommended post-migration check: open every Lunch Alert and every Lunch Supplier
using automatic email sending, and confirm the notification / email time reads as
the hour you actually expect.

## What you gain by migrating

- **One unambiguous format.** Times read as 24-hour values, matching what Odoo uses
  in most other scheduling screens and removing AM/PM mix-ups.
- **Better fit for international teams.** No confusion between 12-hour conventions,
  including the classic "12 AM / 12 PM" trap.
- **Full-day scheduling.** Alerts and supplier emails can be planned anywhere from
  00:00 to 24:00, instead of being capped at the 12-hour range.
- **Less clutter.** One field instead of two on both the alert and the supplier
  forms.
- **Cleaner, shared code.** The time conversion helper now lives in Odoo's standard
  `date_utils`, so custom developments built on top of Lunch rely on a supported,
  maintained utility instead of a module-local one.
