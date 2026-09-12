# fleet migration guide (14.0 -> 15.0)

## What's new for users

Two items of the official 15.0 release notes concern the Community `fleet` addon:

- **New fuel types.** Vehicle models can now be declared as Plug-in Hybrid Diesel, Plug-in Hybrid Essence, Full Hybrid Essence, CNG or Hydrogen, in addition to the existing Diesel, Gasoline, Electric and Hybrid values. Existing records keep their value; you simply have more choices.
- **Fleet Manager on the vehicle.** The Fleet Manager is now assigned on the vehicle itself, instead of being inherited from a higher-level record (a model or a fleet).

The other Fleet release-note items (dashboard filter by car/bike, pivot reporting on vehicles, contracts and services, employee profile from the vehicle form, salary configurator sorting by category) belong to Enterprise add-ons and are **not** included in this Community module.

## Technical data model changes

**Models removed in 15.0** (the "multiple fleet" mechanism of 14.0 was reverted):

- `fleet.category` ("Vehicle Fleet"): name, active, description, tag_ids, manager_id, company_id, color, vehicle_ids, car_count, bike_count, favorite_user_ids, is_favorite.
- `fleet.category.tag` ("Vehicle Fleet Category"): name, color.
- The `fleet_id` field on `fleet.vehicle` disappears with them.

**Field changes on `fleet.vehicle`:**

- `company_id` is no longer computed from the fleet; it is a plain field that defaults to the current company.
- `manager_id` is relabeled "Manager" -> "Fleet Manager" and is no longer derived from the fleet's manager.
- `driver_id` and `future_driver_id` help texts were reworded ("Driver address of the vehicle", "Next Driver Address of the vehicle").
- On `fleet.vehicle.log.contract`, `purchaser_id` (related to the vehicle driver) is relabeled "Current Driver" -> "Driver".

**Harmless rewrite:** the `days_left` computation on contracts was simplified (`diff_time if diff_time > 0 else 0`); the result is identical.

**Pure code reorganisation (no data impact):** the large `fleet_vehicle.py` / `fleet_vehicle_cost.py` files were split into one file per model (`fleet_service_type.py`, `fleet_vehicle_assignation_log.py`, `fleet_vehicle_log_contract.py`, `fleet_vehicle_log_services.py`, `fleet_vehicle_model_brand.py`, `fleet_vehicle_model_category.py`, `fleet_vehicle_odometer.py`, `fleet_vehicle_state.py`, `fleet_vehicle_tag.py`). Models and fields are unchanged; only developers writing upgrade scripts or inheriting those Python modules need to care. No method signature changes were detected.

## How your habits should change

- If you used the 14.0 multiple-fleet screen (Fleet Categories and their tags), it is gone: vehicles are no longer grouped under a fleet. Use vehicle tags, the Fleet Manager field, or saved filters instead, and export your `fleet.category` data before migrating if you need to keep a trace of it.
- Set the Fleet Manager directly on each vehicle; the company of a vehicle is also set directly and no longer follows a fleet.
- On a contract, the driver column now reads "Driver" instead of "Current Driver".
- When creating or updating a vehicle model, have a look at the fuel type list: the new Plug-in Hybrid, Full Hybrid, CNG and Hydrogen values may describe your fleet better than the generic "Hybrid".

## What you gain by migrating

- A cleaner, more accurate vehicle catalogue thanks to the extended fuel taxonomy.
- Per-vehicle Fleet Manager assignment, which matches real organisations where different people manage different vehicles.
- A revamped, maintainable code base (one file per model, coding-guideline compliant), making future Odoo upgrades and custom developments cheaper.
- Full support and security maintenance: 14.0 is out of the supported upgrade path, while 15.0 keeps you on a maintained release.
- No data loss on the core fleet objects (vehicles, models, brands, odometer logs, contracts, services, assignation logs, statuses and tags are all preserved); only the optional 14.0 multiple-fleet layer disappears.
