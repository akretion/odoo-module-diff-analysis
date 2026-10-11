# resource migration guide (18.0 -> 19.0)

This guide covers the Community addon **resource** (working schedules and resource calendars). It is written for functional users and for anyone weighing a migration from 18.0 to 19.0.

## What's new for users

The official 19.0 release notes do not describe the Community `resource` module directly. The only schedule-related entries are **Appointments** ("Flexible appointment scheduling", "Flexible appointments: duration display") and **Planning** ("Auto-plan on flexible schedules"). Those are Enterprise apps that *consume* `resource`; they are not part of the Community edition and nothing from them is delivered by this addon. No other part of the extract concerns working schedules, so there is no release-notes headline for this module.

What 19.0 actually brings to the module comes from the code changes themselves:

- Attendance lines can be defined by a **duration** instead of a start/end time.
- A new **Full Day** period type is available on attendance lines.
- A new calendar mode, "attendance based on duration", centres work hours around midday.
- The attendance list view shows an editable **Duration (hours)** column.

## Technical data model changes

**resource.calendar.attendance**

- Removed fields: `date_from` (Starting Date), `date_to` (End Date) and `resource_id` (Resource). Attendance is now always at calendar level and no longer date-bounded.
- `duration_hours` is now stored, editable and has an inverse: on a duration-based calendar, entering a duration recomputes `hour_from` / `hour_to`.
- `day_period` gains the value `full_day`.
- `hour_from` / `hour_to` now default to 0.
- `_is_work_period()` also excludes section lines.
- `duration_days` returns 1 for a full day.

**resource.calendar**

- New `duration_based` boolean ("Attendance based on duration").
- New action `switch_based_on_duration`.
- New rule: a "Break" (`lunch`) line is rejected on a duration-based calendar (UserError).
- `_check_attendance` and `_check_attendance_ids` are merged into a single `_check_attendance_ids` constraint.
- `_compute_hours_per_day` / `_compute_hours_per_week` now depend on `attendance_ids` and `flexible_hours`; the helpers `_get_hours_per_day()` / `_get_hours_per_week()` no longer receive an attendance list.
- `_get_days_per_week` and `_get_max_number_of_hours` were removed.
- `hours_per_week` is no longer copied on duplication (`copy=False`).
- Interval computation no longer supports per-resource or dated attendance lines.
- Two-week calendars: enabling the mode copies each line into both weeks (`_get_two_weeks_attendance`); changing company also copies `two_weeks_calendar` from the company calendar.

## How your habits should change

The release notes give no habit-level guidance for this module, so the following follows from the model changes above.

- Stop creating attendance lines per employee and stop dating them: those options are gone.
- On duration-based calendars, enter only the duration of each line; start and end times are derived around 12:00 (morning ends at 12:00, afternoon starts at 12:00, a full day is centred on midday).
- Prefer one "Full Day" line over morning + afternoon when it fits.
- Break lines cannot coexist with the duration-based mode.
- Two-week mode still requires exactly one section per week.
- Plan a data cleanup before migrating: existing per-resource and dated attendance records have no equivalent in 19.0.

## What you gain by migrating

- A simpler, more consistent working-time model: one calendar, one set of lines, no leftover dated or per-resource rows.
- Faster data entry: type durations instead of computing start and end times.
- More reliable derived values (hours per day and per week, work time rate, full-time flag) thanks to unified, rounded computations and merged consistency checks — these feed time off, overtime and payroll logic.
- Alignment with where 19.0 is heading: the Enterprise announcements on flexible scheduling and auto-planning build on the same flexible / duration-based scheme this addon now exposes, so migrating now puts your calendars on the right footing.
