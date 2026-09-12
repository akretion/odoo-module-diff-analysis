# hr_homeworking migration guide (16.0 -> 17.0)

This addon lets employees declare where they work each day of the week (office, home, ...) and lets colleagues see it. Odoo 17.0 rewrites how those locations are stored: the goal is to stop creating a database record for every day.

## What's new for users

- **A location per weekday, repeated weekly** — the employee form keeps seven fields (Monday to Sunday). Each now defaults to a standard work location instead of being empty, and the choice repeats every week, with no end date.
- **See where colleagues work** — colleagues can check another employee's location from the Calendar app (and the Employee portal), as the release notes describe for Homeworking.
- Other "Employees" release-notes items (certification report, employee CV, fleet-manager planning, org chart, graph/pivot views) belong to other apps or to Enterprise, so they are out of scope here.

## Technical data model changes

**hr.employee / hr.employee.base**
- `monday_location_id` ... `sunday_location_id` keep their names but now have a default: `_default_location_id()` returns the standard work location `hr.home_work_office`.
- The `create()` and `write()` overrides that generated one `hr.employee.location` record per weekday were removed.
- `_get_worklocation(start_date, end_date)` was rewritten: it returns each employee's weekly defaults (read from the weekday fields) plus their dated exceptions.
- Icon/computation logic now filters on a single `date = today` instead of `start_date` + `weekly = False`.

**hr.employee.location**
- Added: `date` (Date).
- Removed (11 fields): `weekday`, `weekly`, `start_date`, `today_next_date`, `end_date_create`, `removed`, `parent_default_homeworking_id`, `child_removed_ids`, `current_location`, `end_date`.
- Constraint changed: `unique(employee_id, date)` replaces `unique(employee_id, weekly, start_date, current_location)` — at most one exception per employee per day, and no more default-vs-exception rows.
- Removed methods: `_compute_weekday`, `_compute_today_next_date`, `_check_current`, `_save_previous_default_worklocation`, `_next_weekday_date_after_today`, `_removed_worklocation_to_active_worklocation`, `_check_exceptional_work_location`, `add_exceptional_worklocation`, `exceptional_worklocation_to_default_worklocation`, `add_removed_work_location`, `delete_default_worklocation`, `_get_new_worklocation_vals`, `create_default_worklocation`, `_clean_values_to_create`, plus the custom `create()`.
- `_compute_day_week_string` now just returns the weekday name of `date`. No method signature changes were detected.

## How your habits should change

- Set weekly defaults on the employee record (Monday → Sunday fields); no record is created elsewhere. Clearing a field removes the default.
- Exceptions are single days: one record per employee per day. There is no start/end range to maintain; marking an exception as weekly turns it into the weekday default.
- "Removed" days no longer exist as records.
- In Calendar, default days appear as ghost events (computed on the fly), exceptions as real editable events.
- Data: expect weekly defaults to be folded into the employee's weekday fields and dated exceptions to become single-date rows during the upgrade. Check the result on a copy of your database, and review any custom report, import or automation using `weekly`, `start_date`, `end_date` or `removed`.

## What you gain by migrating

- A much lighter database and faster calendars: no more one record per employee per working day.
- A simpler model: seven weekday defaults on the employee, plus at most one exception per day.
- Alignment with the 17.0 Homeworking feature described in Odoo's release notes, including location visibility from the Calendar app and Employee portal.
