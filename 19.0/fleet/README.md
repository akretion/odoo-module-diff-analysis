# fleet migration guide (18.0 -> 19.0)

Scope: the Community `fleet` addon. Odoo 19.0 reworks where the "plan to change" flags live, standardises how vehicle specifications are stored, and adds mileage reporting.

## What's new for users

The official 19.0 release notes mention only one Fleet novelty:

- **Odometer report** – a report of the average mileage per month, computed from a vehicle's odometer records, is now available.

Everything else below comes from the addon's own data model and forms. The 19.0 release notes contain no other Community Fleet item; Fleet entries listed under Localizations belong to other modules and are not part of this addon.

## Technical data model changes

**"Plan to change" flags moved from contacts to vehicles**
- The `fleet` extension of `res.partner` is removed: contact-level `plan_to_change_car` / `plan_to_change_bike` no longer exist.
- On `fleet.vehicle`, these fields are no longer related stored fields; they are plain booleans owned by each vehicle.
- Setting a *Future Driver* now flags that driver's vehicles of the same type (grouped write) instead of one shared contact flag. Promoting the future driver to driver clears the flags on the vehicle.

**Vehicle model / specification revamp**
- `model_year`: free text/Integer → Selection of years from 1970 to the current year, on `fleet.vehicle.model` and `fleet.vehicle`.
- New `range_unit` (km / mi, default km) on the model, propagated to the vehicle.
- New `co2_emission_unit` (g/km or g/mi), computed from `range_unit`, on model and vehicle.
- New `drive_type` on `fleet.vehicle.model`: Front-Wheel (FWD), All-Wheel (AWD), Rear-Wheel (RWD), Four-Wheel (4WD).
- `power` and `horsepower` changed from Integer to Float (decimals allowed) on model and vehicle; the power unit label "Horsepower" becomes "Horsepower (hp)".
- Renamed labels: "Seats Number" → "Seating Capacity", "Doors Number" → "Number of Doors", "CO2 Emissions" → "CO₂ Emissions", "CO2 Standard" → "Emission Standard". New help texts on trailer hitch, doors and emission standard.

**Other changes**
- `fleet.vehicle`: new `action_open_odometer_report()`; `_clean_vals_internal_user()` signature changed.
- `fleet.vehicle.odometer`: new `_compute_driver_id()` – odometer records keep the driver history.
- `fleet.vehicle.model.brand`: new `action_open_brand_form()` (clicking a kanban card opens the brand form).
- Per-field `_compute_*` methods added on `fleet.vehicle` so model values no longer overwrite fields that were manually edited (fixes a reset issue reported when migrating from 18.2).

## How your habits should change

- Maintain *Plan to change car / bike* on each vehicle: the contact form no longer shows these flags, and a change no longer applies to all of a driver's vehicles at once.
- Pick the model year from the list (1970 → current year) instead of typing it; check that existing values are valid years.
- Set range and CO₂ units once on the vehicle model: kilometres imply g/km, miles imply g/mi, and the vehicle inherits them.
- Power and horsepower accept decimal values (e.g. 110.5 kW).
- Renamed labels may affect saved filters, favourites, exports and Studio/custom reports using the old wording.
- Use the new odometer report for mileage-per-month analysis instead of exporting odometer records.

## What you gain by migrating

- Mileage analytics out of the box: average monthly mileage per vehicle from real odometer records.
- A cleaner "plan to change" workflow: flags follow the vehicle, not the driver's contact, and are reset consistently when the future driver takes over.
- Better vehicle catalogue quality: standardised model years, drive type, and consistent metric or imperial units for range and CO₂ — useful for mixed fleets and reporting.
- Manual specification edits (seats, doors, power, colour, category…) are no longer silently overwritten by model defaults after the upgrade.
- Faster navigation from the brand kanban to the brand form, and driver history preserved on odometer records.
- Staying current: 19.0 is the supported release, while fixes on 18.0 wind down over time.
