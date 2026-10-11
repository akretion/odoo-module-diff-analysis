# hr_maintenance migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 release notes do not list any feature that is specific to the *hr_maintenance* addon. The visible improvements come from the base **Maintenance** app that this addon extends, and are available in the Community edition as soon as Maintenance is installed:

- Equipment requests (and work center requests) now have dedicated Gantt views.
- Maintenance stages can be customised per maintenance team.
- General UX clean-up, including a new "Status" widget.

Since *hr_maintenance* only adds HR assignment fields on top of Maintenance equipment records, your equipment screens inherit these improvements, but no HR-specific new feature was announced. Everything else in 20.0 is internal refactoring plus the departure change described below.

## Technical data model changes

On `maintenance.equipment` (PR 215599):

- `equipment_assign_to` was moved to the base *maintenance* module. *hr_maintenance* now extends it with `selection_add` for "Department" and "Employee"; the field stays required with default "Employee", and removing a value resets it to "Other".
- `employee_id` and `department_id` are now computed by `_compute_equipment_assignment_fields` (was `_compute_equipment_assign`); they remain stored and editable.
- `owner_user_id` and `assign_date` are no longer defined in *hr_maintenance*: ownership is now stored and computed in the base Maintenance module and only extended here.
- New hooks: `_get_assign_fields()`, `_get_owner_methods_by_equipment_assign_to()`, `_get_assignment_handlers_by_equipment_assign_to()`.
- New `is_assigned` computation with a `_search_is_assigned()` implementation, so the assigned / not-assigned state is searchable.

On employee departure (PR 253079):

- Removed `hr.employee.departure_do_unassign_equipment` (related field on `version_id`, inherited) and the removed *hr.version* override file of this addon.
- Removed `do_unassign_equipment` ("Free Equipments") from `hr.employee.departure`.
- `action_register()` no longer filters departures on that checkbox: it always unassigns the departing employee's equipment.

## How your habits should change

- **Ending a collaboration:** the "Free Equipments" option is gone. Registering an employee departure now always unassigns their equipment. If you want an item to stay assigned, do it manually afterwards.
- **Owner and assign date:** now driven by the base Maintenance app. The owner still follows the assigned employee's user, or the department manager's user, falling back to the current user — same intent, one shared implementation.
- **Assignment type:** switching "Used By" between Department / Employee / Other behaves as before; the counterpart field is cleared. A selection value that is deleted is reset to "Other".
- **Filtering:** you can filter equipment on the assigned / not-assigned state directly in search views.
- **Custom code:** overrides of `_compute_equipment_assign` or `_compute_owner` must be ported to the new hooks.

## What you gain by migrating

- Equipment ownership and assignment logic is centralised in Maintenance, so HR and Fleet rules stay consistent instead of being duplicated per module.
- Departure handling is fully automatic: no more forgotten equipment when an employee leaves, applied by the date-driven departure procedure.
- Stored, searchable assignment state and owner, enabling reliable reporting and filters on who currently holds what.
- Cleaner extension points for your integrator, plus inherited Maintenance UX improvements (Gantt views, per-team stages, Status widget).
