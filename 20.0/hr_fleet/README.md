# hr_fleet migration guide (19.0 -> 20.0)

`hr_fleet` is the bridge between the Employees and Fleet apps in Odoo Community. Between 19.0 and 20.0 the addon mostly *removes* things: the employee mobility card fields disappear from the generic screens, the "Fleet Manager" activity-plan responsible type is dropped, and releasing the company car on departure becomes automatic.

## What's new for users

The 20.0 release notes say very little that concerns this Community addon. The only Fleet entry touching the employee/vehicle link is **Driver assignment**: when you select or change the car of an employee, the vehicle's assigned driver is kept up to date automatically. The note is written from the Payroll side (an Enterprise app); in Community, what you actually experience is that the driver recorded on the vehicle stays consistent with the car assigned to the employee.

The other release-note items (fleet document auto-sorting, employee document folders, pay-run workflow, payslip UX, dashboards, work entries removal) belong to other apps — mostly Enterprise or other modules — and do not change `hr_fleet`. The user-visible changes below therefore come from the data model clean-up.

## Technical data model changes

**Mobility card removed (3 deleted fields)**
- `hr.employee.mobility_card` (Char, fleet user group) — removed.
- `hr.employee.public.mobility_card` (Char, readonly) — removed.
- `fleet.vehicle.mobility_card` (Char, stored computed) — removed, together with the `_compute_mobility_card` method on `fleet.vehicle` and the `write()` override on `hr.employee` that refreshed it.
- The field now lives only in the Belgian localization (`l10n_be_hr_payroll`), on the employee view used for Belgian companies.

**Activity plans: "Fleet Manager" responsible type removed**
- `hr_fleet` no longer inherits `mail.activity.plan.template`; the `fleet_manager` value of `responsible_type`, the `_check_responsible_hr_fleet` constraint and the `_determine_responsible` override are gone.
- In the same release, responsible types `coach` and `fleet_manager` were removed and launch plans no longer block when the employee is not fully configured: they warn instead and assign the activity to the current user.

**Departure / company car release**
- Removed: `hr.employee.departure_do_unassign_company_car` (related, inherited), `hr.employee.departure.do_unassign_company_car` (Boolean, default = user belongs to the Fleet group) and `hr.version.departure_do_unassign_company_car`.
- The `hr_version.py` file is deleted from the addon.
- `hr.employee.departure.action_register()` now always calls `_unassign_company_car()`; the call is no longer filtered by the removed checkbox.

No new models and no new fields are added by this addon in 20.0.

## How your habits should change

- **Mobility card:** for non-Belgian companies the field no longer appears on the employee form or on the vehicle form. If you relied on it as a free-text badge reference, plan an alternative field or export the existing values before migrating. Belgian companies keep the field through the localization.
- **Activity plans:** if one of your employee plan templates used "Fleet Manager" as responsible, replace it with another responsible type (for example a specific user) before or right after the migration. Launching a plan no longer stops on a missing manager/vehicle: you get a warning and the activity is assigned to you.
- **Departures:** you no longer tick "Release Company Car". The vehicle assignment is released automatically when the departure is registered; you can still choose the date on which the actions apply.
- **Driver assignment:** assigning or changing an employee's car keeps the vehicle driver in sync, so you rarely need to edit the driver manually.

## What you gain by migrating

- Cleaner screens: the mobility card only appears where it is legally meaningful (Belgian companies), instead of on every employee and vehicle.
- Fewer forgotten steps: registering a departure always frees the company car, so no vehicle stays attached to a former employee.
- More robust HR flows: employee launch plans work even when the manager, fleet or user is not fully configured — you get a warning instead of a blocking error.
- A lighter, more maintainable addon: removed inheritance and dead fields mean fewer customizations to review during future upgrades.
- Staying on a supported version, aligned with Odoo 20 Fleet/Employees behaviour and the OCA community tooling Akretion deploys.

*Note: the two remaining behavioural points above (activity-plan responsible types, automatic car release) ship with `hr` / `l10n_be_hr_payroll`; `hr_fleet` mainly adapts to them. Verify Enterprise-only Payroll features separately before planning your migration.*
