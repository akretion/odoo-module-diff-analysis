# fleet migration guide (18.0 -> 19.0)

## What's new for users

- **Odometers report**: a new report shows the average mileage per month, based on your odometer records. It helps spot vehicles that are over- or under-used.
- The official 19.0 release notes say very little about Fleet itself: the only Fleet-specific entry is the odometers report above. Everything else that changes for you comes from the addon rework described below (vehicle model configuration and the "plan to change" flags).

## Technical data model changes

**Vehicle model configuration revamp (PR #197440)**
- `model_year` on `fleet.vehicle.model` and `fleet.vehicle` changed from an Integer to a Selection listing years from 1970 up to the current year.
- New field `drive_type` on `fleet.vehicle.model`: Front-Wheel Drive (FWD), All-Wheel Drive (AWD), Rear-Wheel Drive (RWD), Four-Wheel Drive (4WD).
- New field `range_unit` (km / mi, default km, required) on both `fleet.vehicle.model` and `fleet.vehicle`.
- New field `co2_emission_unit` (g/km or g/mi) on both models: computed from `range_unit`, stored, required.
- `power` and `horsepower` changed from Integer to Float on both `fleet.vehicle.model` and `fleet.vehicle`.
- Labels and help texts updated: `seats` → "Seating Capacity", `doors` → "Number of Doors", `co2` / `default_co2` → "CO₂ Emissions", `co2_standard` → "Emission Standard", power unit option "Horsepower" → "Horsepower (hp)". Tooltips were added for trailer hitch, number of doors and emission standard.

**"Plan to change" flags are no longer contact fields (PR #197812)**
- The `res.partner` inheritance (`fleet/models/res_partner.py`) was deleted: `plan_to_change_car` and `plan_to_change_bike` no longer exist on contacts.
- On `fleet.vehicle` they become plain Booleans instead of fields related to `driver_id.plan_to_change_*`.
- The flags are now written on every vehicle of the same type whose driver matches the future driver, and cleared when the driver change is actually performed. An upgrade script accompanies this change.

## How your habits should change

- Model year is picked from a dropdown (1970 → current year) instead of being typed. Check imported data before the migration.
- Emissions unit follows the range unit: km gives g/km, mi gives g/mi. Always check the unit before comparing CO₂ figures between vehicles or in exports.
- Power and horsepower now accept decimals (e.g. 110.5 kW) and are no longer rounded to whole numbers.
- Field labels changed in forms, list views, filters and exports ("Seating Capacity", "Number of Doors", "Emission Standard", "CO₂ Emissions"). Update saved filters, exports and custom reports using the old names.
- "Plan to change car / bike" is now tracked per vehicle, not per contact. Two vehicles of the same driver can differ, and both flags are reset when the future driver becomes the actual driver. Remove any custom filters or reports on `res.partner.plan_to_change_*`, and review automations that relied on the shared contact value.
- Enrich your vehicle models with drive type and range unit so vehicles inherit consistent data.

## What you gain by migrating

- Cleaner, better documented vehicle data: standardized labels, tooltips, explicit units and a year picker reduce entry mistakes.
- New attributes (drive type, range unit, CO₂ unit) that make fleet and emissions reporting accurate instead of unit-ambiguous.
- Decimal precision on power and horsepower.
- The new odometer-based average mileage per month report.
- Per-vehicle "plan to change" tracking removes the surprising side effect where one contact flag changed every vehicle of that driver.
- You move to a still-supported version and keep receiving fixes; the 18.0 → 19.0 migration of this data is handled by standard upgrade scripts.

*Note: the release notes also mention Fleet in the ESG (carbon) and Mexico Carta Porte sections. Those live in Enterprise / localization apps and are not part of this Community addon.*
