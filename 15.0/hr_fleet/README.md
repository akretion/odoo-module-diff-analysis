# hr_fleet migration guide (14.0 -> 15.0)

This addon links Fleet vehicles to HR employees. Odoo 15.0 reverts the "multiple fleet" mechanism that existed in 14.0 (the "internal" flag on vehicle categories) and simplifies how a vehicle is linked to an employee.

## What's new for users

From the official 15.0 release notes, only one item concerns hr_fleet directly:

- **Employee profile from the vehicle form** — you can open the related employee's profile straight from the vehicle record, without leaving Fleet.

The other Fleet release-note items (new fuel types, dashboard car/bike filter, fleet managers assigned to vehicles, pivot reporting on vehicles/contracts/services) belong to the Fleet core module, not to hr_fleet. The salary-configurator item is an Enterprise feature and is not part of this Community addon. Do not expect them from this migration.

## Technical data model changes

No method signature changes were introduced.

Removed (revert of the multiple-fleet mechanism):
- hr_fleet no longer extends `fleet.category`: the `internal` Boolean (default `True`), the `action_view_vehicles` override, and the `write` override that cleared employee drivers when a category was set to non-internal are all gone.
- The related field `fleet_is_internal` is removed from `fleet.vehicle`, `fleet.vehicle.assignation.log`, `fleet.vehicle.log.contract` and `fleet.vehicle.log.services`.

`fleet.vehicle`:
- `driver_employee_id` and `future_driver_employee_id` are now stored computed fields driven by `driver_id` / `future_driver_id` (lookup of an `hr.employee` whose `address_home_id` matches the partner). They remain tracked.
- New related field `driver_employee_name` (Char).
- New method `action_open_employee()` returning a window action on the employee form.
- Removed: the `_constrain_employee_has_address` constraint, the `_get_driver_history_data` override, and the `action_accept_driver_change` override (employee handling is back in the core method).
- `_update_create_write_vals` is simplified: employee/partner synchronisation no longer depends on the vehicle's category.

Other models:
- `fleet.vehicle.assignation.log.driver_employee_id` is now a related (read-only) field on `vehicle_id.driver_employee_id`, not an independently editable many2one, so log lines follow the vehicle's current driver employee rather than freezing the employee of that period (check your historical data).
- `fleet.vehicle.log.services._compute_purchaser_id` now switches on `purchaser_employee_id` instead of `fleet_is_internal`.
- Label renames: `purchaser_employee_id` on contracts and `driver_employee_id` on odometers go from "Current Driver (Employee)" to "Driver (Employee)".

## How your habits should change

- You can now jump from a vehicle to its driver's employee profile in one click (release notes).
- The release notes say nothing more about this addon. The code changes above nevertheless end the 14.0 habit of classifying vehicle categories as "internal" or not to control whether employees get linked: every vehicle's driver partner now maps to an employee whenever a matching employee address exists.

## What you gain by migrating

- Simpler, more predictable data: the driver/employee link follows the driver partner, with no hidden category-based filtering.
- Less manual maintenance: nothing to keep in sync on categories, and no blocking validation error when an employee has no address.
- Faster navigation: open the related employee directly from the vehicle form.
- Cleaner records: contracts, services and odometers read the driver from the vehicle itself, so "Driver (Employee)" always reflects reality.
- Full compatibility with the 15.0 Fleet core improvements (fuels, dashboard filters, pivot reporting) while staying on Community.
