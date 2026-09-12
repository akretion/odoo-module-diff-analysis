# fleet migration guide (13.0 -> 14.0)

## What's new for users

Odoo 14.0 rebuilds the Fleet cost logic around two simple ideas: **costs are no longer a separate object**, and **services and contracts are the single place where you record what a vehicle costs**.

- **Services and costs merged.** You no longer encode a cost and then attach it to a service: the amount lives directly on the service line.
- **Archive vehicle makes and models.** Models can be deactivated instead of deleted (e.g. a model you no longer buy).
- **Contracts can be recurring or non-recurring.** The recurrence now describes the deal instead of silently creating cost lines.
- **Clearer lists and units.** Odometer units are displayed as "km" / "mi", and list views were reworked for faster reading.

Note: other items in the official Fleet release notes (allowable deductions and disallowed expenses linked to vendor bills) belong to the Accounting app and are not part of this Community addon. Nothing else in the release-notes extract concerns Fleet itself.

## Technical data model changes

**Models removed**
- `fleet.vehicle.cost` — deleted. Contracts and services no longer `_inherit` it, so there is no more `cost_id` link.
- `fleet.vehicle.log.fuel` — deleted; `log_fuel` and `fuel_logs_count` disappear from the vehicle.

**fleet.vehicle.log.contract** — now a standalone model with its own `vehicle_id`, `cost_subtype_id` (contract-type only), `amount` (now Monetary), `date`, `company_id`/`currency_id`, `name` (Char, computed) and a new `service_ids` Many2many for included services. `purchaser_id` is now related to the vehicle driver, `ins_ref` is relabelled "Reference", `cost_frequency` defaults to monthly, `cost_generated` becomes Monetary. Removed: `generated_cost_ids`, `sum_cost`, `cost_amount`, `odometer`, `cost_id`.

**Contract states** — `diesoon` ("Expiring soon") is gone. Only Incoming / In Progress / Expired / Closed remain, and renewal alerts use `open`/`expired`.

**Methods** — removed: `act_renew_contract`, `scheduler_manage_auto_costs`, `_compute_sum_cost`, `_onchange_vehicle`, the `default_get` overrides. Added: `contract_draft`. `run_scheduler` now only handles contract expirations.

**fleet.vehicle.log.services** — gains mail thread/activities, `_rec_name = 'service_type_id'`, Monetary `amount`, a required `service_type_id`, `odometer` compute/inverse, `purchaser_id` related to the driver, and `inv_ref` relabelled "Vendor Reference".

**fleet.vehicle** — new `description` field; `manager_id` is now computed, stored and editable; `cost_count` and `fuel_logs_count` removed.

**fleet.vehicle.model / brand** — models get `active`; changing a model's manager updates the related vehicles. Brands display a `model_count`.

## How your habits should change

- The **Costs** smart button and the **Fuel Logs** menu are gone. Record fuel as a service with the right service type.
- **Recurring costs are no longer posted automatically** by the scheduler. Create the vendor bill or the service log yourself.
- Use **Duplicate** instead of the removed *Renew Contract* action; a contract can also be reset to Incoming.
- Contracts that were "Expiring soon" now show as **In Progress** or **Expired**; update your saved filters.
- Amounts are displayed in the **company currency** (Monetary fields).
- Each odometer update now posts a **message on the vehicle**: better traceability, slightly noisier chatter.

## What you gain by migrating

A much smaller, easier-to-understand data model: fewer technical objects, no duplicated cost records, cleaner services and contracts, currency-aware amounts, archivable models, and better traceability of odometer changes — a solid foundation for cost reporting.
