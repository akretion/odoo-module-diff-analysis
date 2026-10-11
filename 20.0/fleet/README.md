# fleet migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 release notes have very little to say about Fleet as a **Community** feature. The only Fleet entry covers driver assignment: when you select or change a car for an employee, the vehicle's assigned driver is updated automatically. That flow is triggered from the **Payroll** app, which is not part of the Community edition, so it should not be sold to you as a new Community capability. The Fleet-side logic that keeps a driver and the "plan to change" flag in sync still runs in Community, but the automatic assignment described in the notes belongs to the Payroll integration.

For the rest, the release notes extract is silent on this addon. The real changes you will feel during the migration come from the data model rework described below, not from the release notes.

## Technical data model changes

**Vehicle model (fleet.vehicle.model)**
- Removed `color` (Char).
- Removed `trailer_hook` / "Trailer Hitch" (Boolean).

**Vehicle (fleet.vehicle)**
- `color`: was a computed, stored, editable field filled from the vehicle model; it is now a plain Char field with chatter tracking. The compute method `_compute_color` is gone.
- `trailer_hook` / "Trailer Hitch": was computed from the vehicle model; it is now a plain Boolean (default False) with chatter tracking. The compute method `_compute_trailer_hook` is gone.
- Both fields were removed from `MODEL_FIELDS_TO_VEHICLE`, the mapping used to inherit specifications from the vehicle model.
- `plan_to_change_car` and `plan_to_change_bike` (tracked Booleans) are removed and replaced by a single tracked `plan_to_change_vehicle` Boolean.
- `create()` and `write()` were simplified accordingly: concerned drivers are gathered in one set and `plan_to_change_vehicle` is set on their other vehicles; when a future driver is actually assigned, the flag is cleared on the previously assigned vehicles.
- Minor: the help text of "Available From" (`next_assignation_date`) was reformulated.

Upgrade scripts for the field merges are referenced in the odoo/upgrade repository (PR #10197 and #8126).

## How your habits should change

- **Stop configuring colour and trailer hitch on the vehicle model.** They must now be set on each vehicle. New vehicles no longer inherit these two values from the model.
- Existing vehicles keep the value that was last stored on them; nothing is copied from the model anymore. Review your fleet if you used to rely on model-level values, and clean up any inconsistency.
- **Replace "Plan to Change Car" and "Plan to Change Bike" with the single "Plan to Change Vehicle".** Update filters, groupings, list views, automated actions, saved searches, Studio fields and custom reports.
- Any domain such as `[('plan_to_change_car', '=', True)]` must be rewritten to `plan_to_change_vehicle`.
- If you used the two flags to tell cars from bikes in dashboards, use the vehicle type instead: the flag is now independent of the vehicle type.
- If you have custom code or third-party modules that read `model_id.color` or `model_id.trailer_hook`, they will break and must be adapted.

## What you gain by migrating

- **Less duplicated configuration:** vehicle specifications live on the vehicle, where they actually matter, instead of being partly defined on the model and partly overridden.
- **More accurate data entry:** colour and trailer hitch are directly editable on each vehicle, with chatter tracking, so you can record real per-vehicle exceptions.
- **Traceability:** changes to a vehicle's colour are now logged in the chatter, alongside the other tracked fields.
- **Simpler logic:** one "plan to change" flag instead of two type-dependent ones, easier to filter, report on and automate.
- **A maintained platform:** you move to a supported release, with an officially provided upgrade path for these field changes.
