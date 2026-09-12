# hr_presence migration guide (12.0 -> 13.0)

In 13.0, `hr_presence` no longer owns employee presence. The presence state is now defined by the `hr` module and enriched by `hr_attendance`, `hr_holidays` and `hr_presence`. This addon keeps only what the others do not cover: presence deduced from sent emails and from connecting with a valid IP address.

## What's new for users

The Odoo 13.0 release notes describe it as: "Control the presence of employees according to attendance, time off, login, IP addresses or sent emails."

Practically, the employee kanban and form no longer contradict each other: login, attendance, time off and the email/IP checks all feed one aggregated state, and at most one stat button is shown on the employee form (Connected / Last activity, attendance, Absent until...).

The release notes "Employees" section also covers preferences, skills, resume, onboarding, KPI, contracts, schedule, security and documents. Those belong to other apps (several of them are Enterprise-only); none is part of this Community addon, so they are out of scope here.

## Technical data model changes

Moved to `hr` (no longer provided by this addon):
- `hr.employee.hr_presence_state` (present / absent / to_define) is defined as soon as `hr` is installed. `hr_presence` now extends `hr.employee.base`, not `hr.employee`.

Removed fields:
- `hr.employee.last_activity` (was computed from `bus.presence`)
- `hr.employee.hr_presence_control_email_amount` and `hr_presence_control_ip_list`
- `res.company.hr_presence_control_email_amount` and `hr_presence_control_ip_list`
- the `res.config.settings` model contributed by the addon (`hr_presence_control_login`, `hr_presence_control_email`, `hr_presence_control_ip`, `hr_presence_control_email_amount`, `hr_presence_control_ip_list`)

Added fields on `hr.employee`:
- `email_sent` (Boolean, default False)
- `ip_connected` (Boolean, default False)
- `manually_set_present` (Boolean, default False)
- `hr_presence_state_display` (Selection, stored) used for grouping/filtering in the presence kanban

Kept:
- `res.company.hr_presence_last_compute_date` (Datetime)

Behaviour changes:
- `_check_presence()` no longer writes `present` / `absent` from the email and IP rules: it only flags `email_sent` / `ip_connected` (reset once a day per company). Employees on leave are no longer handled here (`hr_holidays` does it) and the login check moved to `hr`.
- New `_compute_presence_state()` aggregates every source and marks the employee present when one of the daily flags is set.
- `action_set_present()` now sets `manually_set_present = True`; a new `write()` override maps a `hr_presence_state_display = 'present'` write to the same flag. Manager access rights are unchanged.
- No method signature changes were detected: `_check_presence`, `_action_open_presence_view`, `action_set_present`, `action_send_sms` and `action_send_mail` keep their signatures.

## How your habits should change

- Login, attendance and time-off rules are now configured in the standard HR settings; the checkboxes that this addon used to show there are gone.
- The email amount threshold and the valid IP list are no longer edited from a dedicated settings screen provided by this addon.
- To mark an employee present by hand, use the same action as before: it is now stored as a manual flag, re-evaluated with the other sources.
- In the presence kanban, group and filter on `hr_presence_state_display`; the underlying `hr_presence_state` is computed.

## What you gain by migrating

- One consistent presence state across chat, attendance, time off, emails and IP, instead of several conflicting indicators.
- Fewer wrongly "absent" employees: a logged-in, checked-in or sufficiently active user is no longer shown red elsewhere.
- The email and IP checks you rely on are kept, now as clean daily flags feeding a single state.
- A simpler addon: no duplicated settings model, no duplicated employee/company fields, easier to maintain on Community.
