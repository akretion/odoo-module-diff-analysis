# hr migration guide (19.0 -> 20.0)

This guide covers the Community `hr` addon. No part of the official 20.0 release notes matched this addon, so everything below comes from the actual 19.0 → 20.0 code changes (models, fields, behaviour, method signatures).

## What's new for users

- **Simpler End of Collaboration.** The departure form no longer asks *which* actions to run (archive user, archive employee, set contract end date...). Those steps always run; you only choose *when*, with a single "Archive Employee On" date (default: the day after the departure date). A scheduled job applies the departure once that date is reached.
- **Departure safeguards.** You cannot start a departure for an employee with no running contract (archive the employee instead). Starting a second departure while one is configured now shows an explanatory dialog asking you to cancel the existing one first.
- **Employee user widget and invitations.** From the employee form you can invite, resend the invitation, copy the invitation link, reset the password, activate/deactivate or archive the related user (archiving triggers the departure flow when the employee is under contract). The form displays the user status: no user / invited / confirmed / archived.
- **Cleaner phone numbers.** Phone fields keep what you typed and display a formatted international version, formatted for the company country.
- **Family and personal data are HR-only again.** Marital status, spouse, children, legal name, place of birth, country of birth and sex were removed from *My Preferences*; only birthday (and its public display) stays self-service.
- **Recruiter field.** On job positions, the Recruiter is now chosen among employees linked to an internal user, instead of any company user.

## Technical data model changes

- `hr.contract.type` → **`hr.employee.type`** (new `employees_count`, `action_open_employees`). `hr.version.contract_type_id` becomes `employee_type_id`, the old `employee_type` selection is dropped, `hr.job.contract_type_id` becomes `employee_type_id`, and `hr.employee` gets `employee_type_id` / `has_country_employee_type`. New `fixed_term` on version and employee.
- **Departures:** `action_at_departure`, `action_other_date`, `do_archive_user`, `do_archive_employee`, `do_set_date_end`, `has_selected_actions` removed, replaced by `action_date` on `hr.employee.departure` and `departure_action_date` on version/employee.
- **`hr.job`:** `user_id` (res.users) → `recruiter_id` (hr.employee, company-checked); `allowed_user_ids` removed.
- **`hr.employee`:** `is_user_active` removed, `user_state` added; `user_id` no longer copied; new phone companion fields (`work_phone_sanitized/formatted`, `mobile_phone_*`, plus HR-only `private_phone_*` and `emergency_phone_*`); inherits `mail.thread.phone`.
- **`hr.employee.public`:** gains related `phone_sanitized/formatted`, `work_phone_*` and `mobile_phone_*` companion fields.
- **`res.users`:** HR employee fields are no longer `related=` but computed through a `field_employee()` helper (sudo only for the current user). `employee_id` is read-only; `SELF_READABLE_FIELDS` / `SELF_WRITEABLE_FIELDS` and the `get_view(s)` overrides are gone. `marital`, `spouse_*`, `children`, `legal_name`, `place_of_birth`, `country_of_birth`, `sex` removed; `monday_location_id`…`sunday_location_id` use the new helper.
- **`res.partner.bank`:** `bank_street`, `bank_street2`, `bank_zip`, `bank_city`, `bank_state`, `bank_country`, `bank_email`, `bank_phone` removed; `currency_symbol` now taken from the employee.
- **Removed abstract models:** `hr.export.mixin`, `hr.export.employee.mixin`.
- **Methods:** `get_formview_action` → `get_record_default_action` (department, employee, version, users); `_get_tz(date=None)`, `_get_first_versions(date_limit=...)`, `_get_calendar_periods(..., check_contract=True)`; `action_create_user(s)` → `action_send_invitation`, `action_reset_password`, `action_copy_invitation_link`, `action_toggle_user_active`; `_onchange_phone_validation_employee` removed; many new batch helpers.

## How your habits should change

- Reconfigure contract types as **Employee Types** and re-map localizations/payroll rules that referenced them.
- Pick a Recruiter who is an employee with an internal user, not a bare user.
- For departures, just choose the date; all follow-up actions run automatically. Cancel an existing departure before creating a new one.
- Read phone numbers from the `*_formatted` / `*_sanitized` companion fields instead of stored formatting.
- Family and payroll-impacting data is edited by HR on the employee form, not by the employee in preferences.
- Bank details (street, city, zip, email, phone) are no longer maintained from HR.
