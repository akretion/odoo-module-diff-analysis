# hr_attendance migration guide (16.0 -> 17.0)

## What's new for users

Attendance has been fully revamped in 17.0.

- **Geolocation**: check-ins and check-outs can store latitude/longitude, plus the browser, IP address and IP-based country/city. A dedicated action opens the position in Google Maps.
- **Public kiosk mode**: the kiosk is reachable from a per-company URL containing a security key, so a shared tablet or browser can be used with no full user login. The key can be regenerated from Settings, and employee PIN identification is configured per company.
- **History on the record**: attendance now includes a chatter. Check-in and check-out changes are tracked.
- **Attendance manager**: each employee can have an Attendance manager, who gets access to the Attendance app and can edit that employee's attendances. Officers are added to (and cleaned from) the officer group automatically.
- **Employee form**: new "hours previously today" and "last attendance worked hours" indicators, plus buttons to open this month's attendances and overtime.
- **New company settings**: "Display Extra Hours" and "Attendance from Systray".

## Technical data model changes

Models:
- `hr.attendance` now inherits `mail.thread` (tracking on `check_in` / `check_out`).
- The `ir.ui.menu` extension that hid the Attendances overview menu is **removed**.

New `hr.attendance` fields: `color`, `overtime_hours`, and `in_` / `out_` pairs of `latitude`, `longitude`, `country_name`, `city`, `ip_address`, `browser`, `mode` (kiosk / systray / manual).
New methods: `action_in_attendance_maps`, `action_out_attendance_maps`; `_attendance_action_change(geo_information=None)`.
`display_name` format changed (duration + HH:mm range).

`hr.employee`: added `attendance_manager_id`, `hours_previously_today`, `last_attendance_worked_hours`. Removed `attendance_scan()`, `attendance_manual()`, `_attendance_action()` (kiosk logic moved out of the model). `last_attendance_id` is now explicitly ordered by `check_in desc`.

`hr.employee.public`: `action_employee_kiosk_confirm()` removed; related fields restricted to the officer group.

`res.company`: added `hr_attendance_display_overtime`, `attendance_kiosk_key`, `attendance_kiosk_url`, `attendance_kiosk_use_pin`, `attendance_from_systray`, plus `_regenerate_attendance_kiosk_key()` and `_action_open_kiosk_mode()`.

`res.config.settings`: `group_attendance_use_pin` removed (replaced by the per-company `attendance_kiosk_use_pin`); new `hr_attendance_display_overtime`, `attendance_kiosk_url`, `attendance_from_systray`, `regenerate_kiosk_key()`.

`res.users`: added `attendance_manager_id`, `display_extra_hours`, `_clean_attendance_officers()`, `action_open_last_month_attendances()`, `action_open_last_month_overtime()`.

Security group: `group_hr_attendance_user` is **renamed** to `group_hr_attendance_officer`; every field/rule using it was updated.

Behaviour change: `hours_last_month` (and its display) now computes hours from the 1st of the **current** month to now, instead of the previous calendar month in 16.0.

## How your habits should change

- The officer group was renamed. Existing Attendance officers and any custom group, rule or server action referencing `group_hr_attendance_user` must be reviewed.
- PIN handling moved: forget the global "Employee PIN" setting — enable employee PIN identification per company, and configure the kiosk through its URL and key.
- Kiosk scanning and PIN validation are no longer done through employee methods. Any customisation of `attendance_scan`, `attendance_manual` or `_attendance_action` must be re-implemented on the new kiosk flow.
- Read "Hours last month" carefully: it is now a current-month-to-date figure, so reports built on it need checking.
- The Attendances overview menu is no longer hidden from attendance officers.

## What you gain by migrating

- Proof of presence: each check-in/out can carry GPS coordinates and device/IP information, visible on a map.
- A shared-tablet kiosk working without user accounts, protected by a key you can revoke at any time.
- Traceability of manual edits thanks to the chatter.
- Clearer responsibility: an Attendance manager per employee, with automatic officer rights.
- Reliable hours and overtime figures on the employee form, with one-click access to the month's attendances and overtime.
