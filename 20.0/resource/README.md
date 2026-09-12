# resource migration guide (19.0 -> 20.0)

## What's new for users

No official 20.0 release notes extract covers the `resource` addon, so this section is drawn from the code changes themselves.

This addon defines working schedules (`resource.calendar`) behind employees, time off, planning and timesheets. In 20.0 the scheduling model is refreshed:

- Schedules are now described by real worked hours per day (`hour_from`/`hour_to` or a duration), with no morning/lunch/afternoon blocks. The Break period disappears: time not worked must be excluded by shortening the day.
- The 2-weeks calendar is removed. A calendar now describes a single repeating week.
- "Flexible" calendars are removed. An employee with no calendar simply declares Hours per Week and Hours per Day to keep a flexible schedule.
- The timezone no longer lives on the calendar. It is taken from the employee/resource, or from the new Timezone field on the company.
- Attendance lines become date-aware: create a one-off attendance for a date, start/adjust a recurrence, exclude a single occurrence and stop a recurrence. This covers irregular patterns that used to require week A/week B.

## Technical data model changes

`resource.calendar` — removed fields: `two_weeks_calendar`, `attendance_ids_1st_week`, `attendance_ids_2nd_week`, `schedule_type` (Flexible/Fully Fixed), `duration_based`, `flexible_hours`, `two_weeks_explanation`, `tz`, `tz_offset`. Removed methods include `switch_calendar_type`, `switch_based_on_duration`, `_get_two_weeks_attendance`, `_get_hours_for_date`, `_search_work_time_rate`, `copy_data`. Added `is_fulltime`, `_calendar_clean_up`, `get_attendances`, `is_calendar_referenced`, `_get_attendances_by_date`, `_get_reference_hours_per_day`, plus `create`/`write` overrides.

`resource.calendar.attendance` — removed fields: `name`, `duration_days`, `week_type`, `two_weeks_calendar`, the `lunch` value of `day_period`. `duration_based` is now a stored compute (true when `hour_from` and `hour_to` are both 0); `day_period` is computed. New recurrence API: `create_ad_hoc`, `create_new_recurrency`, `exclude_occurence`, `exclude_multiple_occurences`, `stop_recurrency`, `_convert_single_occurrence_recurrencies`, `_clean_excluded_occurrences`, plus a `recurrency_until` field.

`resource.resource` and `resource.mixin` — added `hours_per_week` and `hours_per_day` (with a consistency constraint), `_get_resources_per_tz`, and `_get_calendar_data_at` (replacing `_get_calendar_at`). `resource.mixin.list_leaves()` was removed.

`res.company` — added a `tz` (Timezone) field.

Method signatures: `_attendance_intervals_batch`, `_leave_intervals_batch`, `_unavailable_intervals_batch` and `_work_intervals_batch` now take `resources_per_tz` instead of `resources`/`tz`/`lunch`; `_get_unusual_days` takes an extra `resource` argument.

## How your habits should change

- Configure only the hours (from/to or duration) for each weekday; stop creating morning/afternoon/lunch rows.
- Used a Flexible calendar? Delete it and set Hours per Week / Hours per Day on the employee.
- Used the 2-weeks calendar for alternating schedules? Rebuild it with date-based attendance changes (one-off, recurrence, exclusion).
- Set the timezone on the company or the employee, not on the calendar.
- Lunch is no longer subtracted; shorten the daily attendance instead if you need a break.

## What you gain by migrating

- A single, clear weekly schedule model that is easier to read and maintain.
- Date-based exceptions instead of fragile week A/week B calendar sections.
- Fewer fields on calendars and no duplicated timezone data.
- Flexible workers modelled without a "flexible calendar" workaround.
- Conversion handled by Odoo's upgrade scripts (`upgrade#9030`), so 19.0 attendances are migrated automatically.
