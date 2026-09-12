# hr_attendance migration guide (12.0 -> 13.0)

## What's new for users

- **Attendance joins the new employee profile.** The employee form becomes a full profile, and attendance is part of it: a stat button shows the number of hours worked last month.
- **Employees can see their own data.** A user linked to an employee can read their own profile — including attendance status and hours worked last month — from the Preferences menu, without being granted HR access rights.
- **Preferences and employee data in one place**: user preferences and employee information are now managed on the same screen.
- **Presence** (through the presence module): employee presence can be computed from attendance (check-in/check-out), time off, logins, IP addresses or sent emails.

Other 13.0 release-note topics (skills, resume/CV, contracts, onboarding, turnover KPIs, schedules, documents, payroll security split) belong to other hr addons and do not change hr_attendance.

## Technical data model changes

Removed from `hr.employee` in hr_attendance:

- `barcode` ("Badge ID") and `pin`. They are now defined on the base `hr.employee` model and shared with Point of Sale, so hr_attendance no longer provides them. The related random defaults (`_default_random_barcode`, `_default_random_pin`), the `barcode_uniq` SQL constraint, the digit-only PIN check (`_verify_pin`) and the `_init_column` override that back-filled individual values are all removed.
- `manual_attendance` (Boolean, computed with inverse). It only added/removed the employee's user in the "Manual Attendance" group, which was confusing on the employee form.

Added:

- `hr.employee.hours_last_month`: computed Float, the sum of the employee's worked hours over the previous calendar month.
- `res.users.hours_last_month` and `res.users.attendance_state`: related fields pointing to the employee, declared in `SELF_READABLE_FIELDS` so users can read their own attendance information (new file `models/res_users.py`).

No method signature changes.

## How your habits should change

- Badge IDs and PINs are no longer configured from attendance/kiosk settings: manage them on the employee record (hr), where they are also used by Point of Sale. Check your existing badges and PINs after migrating.
- There is no more "Manual Attendance" checkbox on the employee form: give the "Manual Attendance" group to the relevant users through the standard group management screens.
- To check someone's attendance figures, open their employee profile; to check your own, use your Preferences.

## What you gain by migrating

- A consolidated employee profile where attendance is immediately visible, including hours worked last month.
- Employee self-service: users read their own attendance state and hours without extra HR access rights.
- A cleaner, non-duplicated setup: one badge/PIN per employee, shared with Point of Sale, and manual attendance driven by standard groups rather than a form checkbox.
- Attendance data that can feed presence detection.
