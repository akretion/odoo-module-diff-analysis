# hr migration guide (19.0 -> 20.0)

## What's new for users

- **Employee directory**: in a multi-company database, all internal users can now see the employees of all companies, without needing HR rights everywhere.
- **Remote Work is merged into the Employee app**: weekly work locations are managed on the employee form only; the separate Remote Work module is gone.
- **Variable working schedules**: working schedules can be encoded from the calendar mode to describe periods of variable work time, instead of only fixed weekly attendances.

Those are the only release-note items that touch this addon; everything below comes from the code changes themselves.

## Technical data model changes

**Working schedule becomes mandatory**
- `hr.version.resource_calendar_id` and `resource.resource.calendar_id` are now required (default: company calendar). "Flexible" no longer means "no calendar": it is a calendar of the new *undefined* type with no predefined slots.
- `hours_per_week` / `hours_per_day` are removed from `hr.employee`, `hr.version` and `resource.resource`; `resource.calendar` becomes the single source of truth. Their 0–168 / 0–24 checks move with them.
- `is_flexible` / `is_fully_flexible` are no longer stored Boolean fields: they become `_is_flexible()` / `_is_fully_flexible()` methods. On `hr.employee` they accept an optional `date` (defaults to today), so leaves, overtime, planning slots, timesheets and appointments resolve flexibility against the calendar active on that date.
- `hr.employee.public.hours_per_week/_day` are computed from the employee's calendar. `_get_days_per_week`, `_get_hours_per_week/_day` and the `hours_per_week`/`hours_per_day` auto-computations are dropped, as is the fallback to the company calendar.

**User and employee fields**
- On `res.users`, employee-derived fields (job title, phone, mobile, work email, private address, tags, bank accounts, barcode, PIN, work location, weekly location fields, notes, visa, emergency contacts…) are no longer related fields but computed fields, writable by the user on their own record via sudo. Non-HR users can therefore maintain their own preferences again (this also fixes editing your own weekly work locations).
- `hr.employee.user_state` (related to `user_id.state`) replaces `is_user_active`.
- Light users: `action_create_user` / `action_create_users` are replaced by `action_send_invitation`, `action_reset_password`, `action_copy_invitation_link` and `action_toggle_user_active`.
- Family/personal data (marital status, spouse, children, legal name, place and country of birth, sex) is removed again from My Preferences.

**Employee type**
- `hr.contract.type` is replaced by the new `hr.employee.type` model; `contract_type_id` becomes `employee_type_id` on `hr.version` and `hr.job`; the fixed `hr.version.employee_type` selection is removed. `has_country_contract_type` becomes `has_country_employee_type` and `hr.version.fixed_term` is added.

**End of collaboration**
- `action_at_departure`, `action_other_date`, `do_archive_user`, `do_archive_employee`, `do_set_date_end` and `has_selected_actions` are replaced by a single `action_date` field: all departure actions now run automatically at that date.

**Misc**
- Phone fields gain sanitized/formatted companions (`work_phone_formatted`, `mobile_phone_sanitized`…); `hr.employee` inherits `mail.thread.phone`.
- `hr.job.user_id` becomes `recruiter_id` (an employee, restricted to employees with recruiter rights).
- `res.partner.bank` bank_* related fields and the `hr.export.mixin` / `hr.export.employee.mixin` models are removed.

## How your habits should change

- Always assign a working schedule to an employee or contract: empty is no longer valid. For flexible staff, create a flexible (undefined-type) calendar instead of leaving the field empty.
- Manage employee types as data: create `Employee Type` records rather than picking from a fixed list.
- End of collaboration: no more checkboxes for archiving the user, ending the contract, etc. Only choose the date; the rest happens automatically.
- Onboard employees with the invitation widget (send/resend invitation, copy link, deactivate/reactivate) instead of the old "Create User" wizard.
- Marital status, dependents and other payroll-sensitive data are HR-only again: employees cannot edit them from My Preferences.
- Phone numbers are kept raw in the field and displayed formatted; export/report on the formatted companions if needed.
- Users without HR rights now see the employee directory of every company in the database.

## What you gain by migrating

- A single, reliable source of truth for working hours, ending mismatches between employee, contract, resource and company calendars.
