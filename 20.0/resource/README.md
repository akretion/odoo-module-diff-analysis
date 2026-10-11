# resource migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 release notes cover mostly the Appointments and Planning apps. Only two items touch this addon:

- Faster, more accurate availability: resource and date availability calculations were optimized, so bookings, shifts and time slots compute quicker.
- Multiple resources can be assigned to one shift in Planning, built on the reworked working-schedule engine.

Everything else in those sections (accessory products, WhatsApp shifts, Field Service maps and routing, worksheets, customer ratings, industry packages) belongs to Enterprise apps and is not part of this Community addon. The release notes do not describe the working-schedule rework itself — that is covered below.

## Technical data model changes

**resource.calendar**
- New `calendar_type` selection: `fixed` (weekly repeating pattern), `variable` (attendances on specific dates, with recurrencies), `undefined` (no predefined slots; the resource works freely, optionally up to an hours target). Default `fixed`.
- New fields: `days_per_week`, `reference_calendar_id`.
- `hours_per_week` / `hours_per_day` become the single source of truth (moved off `resource.mixin` / `resource.resource`, where they are now read-only related fields). They are user-editable on variable/undefined calendars, validated 0–168 and 0–24.
- Removed: `two_weeks_calendar`, `two_weeks_explanation`, `attendance_ids_1st_week`, `attendance_ids_2nd_week`, `schedule_type`, `flexible_hours`, calendar-level `duration_based`, `tz`, `tz_offset`.
- Removed methods: `switch_calendar_type`, `switch_based_on_duration`, `get_attendances`, `_get_working_hours`, `_check_overlap`.
- New helpers: `_get_working_attendances`, `_get_attendances_by_date`, `_calendar_clean_up`, `is_calendar_referenced`, `_is_flexible`, `_is_fully_flexible`.
- Company timezone replaces the calendar timezone: new `res.company.tz`; on the admin's first login, the user timezone now updates the company.

**resource.calendar.attendance**
- New: `date`, `recurrency`, `recurrency_type`, `recurrency_interval`, `recurrency_end_type`, `recurrency_until`, `recurrency_excluded_occurences`.
- `hour_from` / `hour_to` and `duration_based` are now computed and stored.
- Removed: `name`, `duration_days`, `day_period`, `week_type`.
- Lunch periods no longer exist: time spanning lunch counts as work.

**resource.resource**
- `calendar_id` is now **required**. A flexible resource is a resource with an `undefined` calendar, not a resource without a calendar.
- `_is_flexible()` / `_is_fully_flexible()` are methods (no longer stored booleans); on employees they accept a date, so flexibility is resolved against the version actually active on that date.

**Batch APIs**
- `_attendance_intervals_batch`, `_leave_intervals_batch` and `_work_intervals_batch` now take `resources_per_tz` instead of `resources` + `tz`.

## How your habits should change

- Two-week calendars are gone. Build multi-week rotations in a **variable** calendar, using dated attendances or recurrencies (every N days/weeks, with an end date and excluded occurrences).
- Flexible schedules are now an **undefined** calendar: every employee and resource is assigned a working schedule.
- Set hours per week/day on the **working schedule**, not on the employee or resource — those fields are read-only there.
- Set the timezone on the company (or on the resource), no longer on the calendar.
- Attendance lines no longer carry a name, a day period or a lunch slot, so clean up any custom reports or imports relying on them.

## What you gain by migrating

- One clear concept per schedule type — fixed, variable, undefined — instead of two-week flags, schedule types and flexible-hour booleans.
- True rotating/irregular patterns through recurrencies, which the old two-week mode could not express.
- A single source of truth for hours per week and per day, so HR, Manufacturing work centers, Planning and Appointments all agree.
- No more silent fallback to the company calendar: the working schedule is always defined, and flexibility is evaluated against the right employee version on the right date.
- Faster, more accurate availability, leave and overtime computations.
