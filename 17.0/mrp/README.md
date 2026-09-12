# mrp migration guide (16.0 -> 17.0)

## What's new for users

The official Odoo 17 release notes covering manufacturing only mention the new Shop Floor application (work orders) and the Industries data modules. Shop Floor work orders belong to Odoo Enterprise, so that app is not part of this Community addon, and the Industries modules are unrelated to `mrp`. In short: the release notes say nothing functional about the Community Manufacturing app, so the real user-visible changes come from the code.

- **One pair of dates per operation.** Planned and actual dates are merged: a Manufacturing Order and each work order now have a single **Start** and **End** date, holding first the date you planned, then the date you actually started/finished.
- **Lead times are defined on the Bill of Materials**, not on the product: *Manufacturing Lead Time* and *Days to prepare Manufacturing Order*.

## Technical data model changes

### Dates merged (`mrp.production`, `mrp.workorder`)

Removed from `mrp.production`: `date_planned_start`, `date_planned_finished`, `production_duration_expected`, `production_real_duration`.

Added: `date_start` (required, indexed, default = now or deadline − 1h; "date you plan to start production or date you actually started"), `date_finished` (stored computed, default = start + 1h), `duration_expected` and `duration`. The model order is now `priority desc, date_start asc, id` and its `_date_name` is `date_start`.

Removed from `mrp.workorder`: `date_planned_start`, `date_planned_finished`, and the former separate `date_start`/`date_finished`. They are replaced by `date_start`/`date_finished`, computed from `leave_id` with inverse `_set_dates`. Renamed methods: `_compute_dates`, `_set_dates`, `_onchange_date_start`, `_calculate_date_finished`; `production_date` now relates to `production_id.date_start`. All dependent logic follows: stock moves, components availability, forecasted issue, "late MO" counters, work center counters, planning/unplanning, `button_start` / `button_finish`.

### Lead times moved to the BoM

Removed from `product.template` / `product.product`: `produce_delay` (*Manufacturing Lead Time*) and `days_to_prepare_mo` (*Days to prepare MO*). Added to `mrp.bom` with the same labels, defaults and help texts.

`action_compute_bom_days` now lives on `mrp.bom` (one computation per BoM); the product-level helpers are gone.

Orderpoints: for *Manufacture* rules, `days_to_order` is read from the product's first BoM (`variant_bom_ids` or `bom_ids`), and is 0 when no BoM exists.

Signatures: `StockRule._prepare_mo_vals` and `_get_date_planned` now receive a BoM instead of a product (`_get_date_planned(bom_id, values)`); `MrpProduction._get_date_planned_finished()` was removed.

## How your habits should change

- **Set lead times on the BoM.** If a product has several BoMs (variants, kits, subcontracting), choose the right one: scheduling and replenishment use the first BoM found.
- **Stop looking for separate scheduled/actual dates.** Enter the Start date; the End date is computed, then overwritten by reality when you process the order.
- You can no longer unplan a single work order (unplan the MO instead), and done or cancelled MOs cannot be rescheduled.
- **Review anything custom** (imports, saved filters, spreadsheets, custom reports, automation rules) referencing `date_planned_start`, `date_planned_finished`, `production_duration_expected`, `production_real_duration`, or the product fields `produce_delay` / `days_to_prepare_mo`.

## What you gain by migrating

- Clearer scheduling: one Start/End per MO and per operation, no duplicated planned/actual columns that can diverge.
- Lead time configured where it belongs (the BoM), including multi-level and subcontracted manufacturing, and correctly picked up by orderpoints and the scheduler.
- Consistent duration naming (`duration_expected`, `duration`), aligned with other Odoo apps.
- A maintained version: 16.0 keeps the product-level lead times and the dual-date model, which we cannot backport.
