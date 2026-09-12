# mrp migration guide (18.0 -> 19.0)

## What's new for users

The release notes we received for 19.0 cover localizations only — they contain no Manufacturing (mrp) chapter. Everything below comes from the addon's own commits.

**One MO, many serial numbers.** A manufacturing order is no longer limited to one finished lot/serial. Lot-tracked products keep a single lot (selected or auto-generated, shown directly on the MO). Serial-tracked products generate their serials through a dedicated wizard, with a smart button showing the count and a Clear button to reset. On Shop Floor you are redirected to that wizard.

**Free choice of units of measure.** Unit categories are gone. BoM lines, by-products and MOs can use any unit (buy in pieces, sell in kilograms). Selection is filtered by a new "allowed units" field built from the product's unit, its packagings and its vendors' units.

**Simpler operation documents.** Worksheet type, description, PDF and Google Slide fields are removed from operations and work orders. Keep instructions (text) and one PDF at quality-check-point level.

**Manufacturing security lead time removed** from settings. Use the BoM's "days to prepare MO", vendor lead times and company "days to purchase".

**Planning and costing.** Work center capacities reworked, operation durations computed more consistently, operation cost estimation, blocked work centers visible in the Gantt view, and "Plan Orders" now lists work orders from alternative work centers.

**Also:** standard batch size on BoMs, more reliable by-product quantities, the final product can be used as a component, replenishment rules can use the BOM or pricelist unit as ordering multiple, and a redesigned product catalog.

## Technical data model changes

- **mrp.production**: `lot_producing_id` → `lot_producing_ids` (many2many); added `serial_numbers_count`, `allowed_uom_ids`, `production_group_id`, `reference_ids`; removed `product_uom_category_id` and `procurement_group_id`; `action_mass_produce` removed, `action_generate_serial(workorder=)` plus new `action_view_serial_numbers` / `action_clear_lot_producing_ids`.
- **mrp.workorder**: `finished_lot_id` → `finished_lot_ids`; worksheet fields and `_update_finished_move` removed.
- **stock.move**: `order_finished_lot_id` → `order_finished_lot_ids`; added `production_group_id`; `is_done` removed.
- **mrp.bom / mrp.bom.line / mrp.bom.byproduct**: UoM category fields and `onchange_product_uom_id` removed, `allowed_uom_ids` added; batch-size validation added.
- **mrp.routing.workcenter**: worksheet fields and `note` removed; capacity/duration methods reworked around `_compute_cost`.
- **mrp.unbuild**: `lot_id` is no longer computed from the MO.
- **New models**: `mrp.production.group` (name, productions, child/parent groups) and `stock.reference`, which replaces `procurement.group`; work center capacities become their own records.
- **res.company / res.config.settings**: `manufacturing_lead` and `use_manufacturing_lead` removed.
- **Signatures**: `create(vals_list)`, `write(vals)`, `_action_confirm(create_proc=)`, `_split_productions(skip_procurement=)`, `_get_capacity(product, unit, default_capacity=)`; `toggle_active` replaced by `action_archive`/`action_unarchive`.

## How your habits should change

- Produce serials through the wizard and review them via the smart button; do not expect a single serial per MO.
- Ignore the old UoM-category warning — it no longer exists; pick any unit offered in the filtered list.
- Attach instructions/PDF to the quality check point, not to the operation.
- Remove security lead time from your planning; use BoM "days to prepare MO" and vendor delays.
- Backorders and child MOs are grouped by production group/references instead of a procurement group on the MO.
- Producing several units of a serial product in one work order is now allowed.

## What you gain by migrating

- Serial traceability at the scale of your real production runs: several serials per MO, one wizard, one smart button.
- Real UoM flexibility (pieces vs kilograms) without category constraints or workarounds.
- Cleaner operation documentation: one instruction text and one PDF where it matters.
- Simpler lead-time model and better planning/costing visibility (capacities, durations, operation cost, alternative and blocked work centers).
- A modernized data model (references, production groups) that keeps future 19.0 features in scope.
