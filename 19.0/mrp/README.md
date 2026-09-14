# mrp migration guide (18.0 -> 19.0)

## What's new for users

The Odoo 19.0 release notes we received cover Localizations (country accounting, payroll, e-invoicing) only. Nothing there concerns Manufacturing (mrp), so no release-note feature is highlighted here; the user-visible changes below come from the addon's own history.

- **Several serials per Manufacturing Order.** A MO can now produce more than one serial number instead of exactly one. Serials are generated from the MO (a wizard when several are needed) and listed behind a "Serial Numbers" smart button; on the shop floor the operator is taken to that wizard. For lot-tracked products you still set one lot, shown on the MO, with a Clear button. The single-MO "Mass Produce" flow is gone.
- **Simpler operation documents.** Worksheet types and Google Slide URLs are gone from operations; documentation is now a description plus one PDF, handled at the quality point level.
- **Manufacturing Security Lead Time removed.** The company-level lead time and its setting disappear.
- **Free choice of units of measure.** Any unit allowed on the product (its own unit, its packagings, its vendors' units) can be used on MOs and BoMs.
- **References instead of procurement groups.** MOs, moves and pickings are linked through reference documents and a production group that keeps child, parent and backorder MOs together.

## Technical data model changes

- `procurement.group` is replaced by `stock.reference`; `mrp.production.procurement_group_id` becomes `reference_ids` (Many2many), as does `stock.move.group_id`.
- New model `mrp.production.group` (`name`, `production_ids`, `child_ids`, `parent_ids`) and new `mrp.production.production_group_id` / `stock.move.production_group_id` fields. Child, source and backorder counts, picking links and backorder numbering use it. `stock.picking.production_ids` becomes a related One2many with a related `production_group_id`.
- `mrp.production.lot_producing_id` becomes `lot_producing_ids` (Many2many) with a new `serial_numbers_count`. `mrp.workorder.finished_lot_id` → `finished_lot_ids`; `stock.move.order_finished_lot_id` → `order_finished_lot_ids`. `mrp.unbuild.lot_id` is no longer computed from the MO and is restricted by a related `lot_producing_ids`.
- Removed from `mrp.routing.workcenter`: `worksheet_type`, `note`, `worksheet`, `worksheet_google_slide`; the related worksheet fields and `operation_note` go from `mrp.workorder`.
- Removed: `res.company.manufacturing_lead` and the `use_manufacturing_lead` setting.
- Removed `product_uom_category_id` on `mrp.bom`, `mrp.bom.line`, `mrp.bom.byproduct` and `mrp.production`; new computed `allowed_uom_ids` fields drive unit domains.
- API: `_can_produce_serial_number` → `_can_produce_serial_numbers`; `_is_finished_sn_already_produced` → `_are_finished_serials_already_produced`; `_prepare_procurement_values` loses its `group` argument. Removed: `_prepare_procurement_group_vals`, `_set_lot_producing`, `action_mass_produce`, `MrpWorkorder._update_finished_move`. `action_generate_serial` accepts a `workorder`.

## How your habits should change

- Do not expect one serial per MO: generate serials before marking a serial-tracked MO done. One lot per MO is still enforced.
- Procurement groups are gone from MOs, moves and pickings. Trace a make-to-order sale to its MO, sub-MOs and backorders via references and the production group.
- Merging moves into existing pickings is no longer group-driven: use batch/wave transfers, and the partner setting grouping RFQs by scheduled date.
- Plan with BoM manufacturing lead time and "days to prepare MO" rather than a company security lead time.
- Prepare operation documentation as a description plus one PDF.
- Units of measure no longer need a shared category; product packagings and vendor units now define what is selectable.

## What you gain by migrating

- Serialised production in fewer documents: one MO covers a whole series of serial numbers.
- Clearer genealogy: one production group connects a sale, its MO and all parent, child and backorder MOs.
- References give a flexible, document-agnostic link between Inventory and MRP documents.
- Lighter configuration: fewer worksheet options, no security lead time, and simpler unit-of-measure rules that allow buying and selling a product in genuinely different units.
