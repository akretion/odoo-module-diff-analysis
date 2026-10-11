# mrp migration guide (18.0 -> 19.0)

## What's new for users
The supplied 19.0 release-notes extract has no MRP/manufacturing section; it covers localizations and other apps. No Enterprise-only feature is claimed as Community. User-visible MRP changes come from the module diff:
- Produce multiple serial numbers in one Manufacturing Order (MO).
- MO links use References and a new Production Group instead of Procurement Group.
- UoM categories are removed: BoMs, MOs and by-products can use allowed units without category warnings.
- Manufacturing security lead time is removed.
- Operation-level worksheets are simplified/removed.

## Technical data model changes
- Procurement group replaced by stock.reference:
  - `mrp.production`: `procurement_group_id` removed; `reference_ids` (M2m `stock.reference`); `production_group_id` (M2o `mrp.production.group`).
  - New `mrp.production.group`: `name`, `production_ids`, `child_ids`, `parent_ids`.
  - `stock.move`: new `production_group_id`, `reference_ids`.
  - `stock.picking`: `production_ids` becomes One2many via `move_ids.production_group_id.production_ids`; new related `production_group_id`.
  - `stock.reference`: new `production_ids` Many2many.
  - Child/source/backorder logic now uses `production_group_id`.
- Multiple serials per MO:
  - `mrp.production.lot_producing_id` (M2o) -> `lot_producing_ids` (M2m); new `serial_numbers_count`.
  - `mrp.workorder.finished_lot_id` -> `finished_lot_ids`; `stock.move.order_finished_lot_id` -> `order_finished_lot_ids`.
  - `mrp.unbuild.lot_id` no longer computed from `mo_id`; domain limited to `lot_producing_ids`.
  - Renames: `_can_produce_serial_number` -> `_can_produce_serial_numbers`; `_is_finished_sn_already_produced` -> `_are_finished_serials_already_produced`. `action_generate_serial` may open a serial wizard; `action_mass_produce` removed; `action_view_serial_numbers` and `action_clear_lot_producing_ids` added.
- UoM categories removed:
  - `product_uom_category_id` removed from BoM, BoM line, MO and by-products.
  - `allowed_uom_ids` added on `mrp.production` and `mrp.bom.line`; UoM domains use it. Category onchange warnings/forcing removed.
- Lead times:
  - `res.company.manufacturing_lead` removed; `manufacturing_lead` and `use_manufacturing_lead` settings removed; stock rules no longer add “Manufacture Security Lead Time”.
- Quality/worksheet:
  - `mrp.routing.workcenter`: `worksheet_type`, `note`, `worksheet`, `worksheet_google_slide` removed.
  - `mrp.workorder`: related `worksheet`, `worksheet_type`, `worksheet_google_slide`, `operation_note` removed.
- Signature change: `stock.warehouse.orderpoint._prepare_procurement_values` no longer accepts `group`.

## How your habits should change
The supplied release notes give no MRP-specific habit changes. Practical changes:
- Use References and Production Groups for MO traceability; Procurement Group is gone from MOs.
- Update filters/reports/automations based on `procurement_group_id` to use `production_group_id` / `reference_ids`.
- For serials, generate serial numbers via the wizard and produce many in one MO. For lots, only one lot per MO is allowed. Shop Floor redirects to the serial wizard.
- Unbuilds: choose a produced lot/serial from the MO’s produced lots; it is not auto-filled.
- Review BoM, MO and by-product UoMs against `allowed_uom_ids`; stop expecting category restrictions.
- Plan lead times via BoM produce delay/days to prepare MO, vendor lead times and company days to purchase; no manufacturing security lead.
- Use quality-point instructions/PDF instead of operation worksheet fields where available.

## What you gain by migrating
- Better serialized manufacturing throughput: one MO can produce many serials, reducing MO splitting.
- Clearer traceability: References and Production Groups link MOs, moves, pickings, backorders and child MOs.
- More flexible units of measure: manufacture, buy and sell in practical units without category blocks.
- Simpler quality documents: fewer obsolete worksheet types.
- Explicit lead-time planning: no hidden security lead time.
- Aligned data model: matches Odoo 19 stock flows (`stock.reference`, production groups) and eases future upgrades.
