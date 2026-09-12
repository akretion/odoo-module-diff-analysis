# mrp_subcontracting migration guide (12.0 -> 13.0)

## What's new for users
The 13.0 release notes state it plainly for this module: **define subcontracting BoMs to track components at your subcontractor's place and receive finished products.**

Concretely, in 13.0:
- Subcontracting configuration is simplified. You no longer flag a contact as a "Subcontractor" partner type. You just add the vendor on the subcontracting BoM, and Odoo handles the rest.
- The receipt of a subcontracted product now looks like a normal receipt. If nothing is tracked, no extra button appears and the receipt is recorded automatically.
- When components are tracked (lots/serial numbers), a "Register components" button appears and opens the Produce wizard. You must record all productions before you can edit the stock move lines of the subcontracted product.
- You can inspect the raw materials consumed at the subcontractor straight from the receipt, via a dedicated "Raw Materials for <product>" view.

Note: the other Manufacturing notes from 13.0 (BoM version / ECO report, work order timers, MPS rework, work center substitution, worksheets) do **not** concern this addon and are not described here; several of them are Enterprise-only.

## Technical data model changes
**res.partner**
- Removed: the `type` selection value `subcontractor`, the `bom_id` Many2one to `mrp.bom`, the name suffix, the onchange that pushed the subcontracting location into `property_stock_customer` / `property_stock_supplier`, and the `create`/`write` overrides.
- Added: `property_stock_subcontractor` (Many2one `stock.location`, `company_dependent`), used as source **and** destination when sending goods to this contact.

**mrp.bom**
- `subcontractor_ids` changed from `One2many('res.partner', 'bom_id')` with a domain on the partner type to `Many2many('res.partner', 'mrp_bom_subcontractor')`. A partner can now be linked to several BoMs, and no partner domain is applied.

**res.company**
- `_create_subcontracting_location()` now also creates the `ir.property` record behind `property_stock_subcontractor` for each company, and sets `subcontracting_location_id` by assignment.

**stock.move**
- Removed: `subcontract_components_ids` (computed / inversed One2many of `stock.move.line`).
- Added: a clearer label on `is_subcontract`, `show_subcontracting_details_visible` (computed), `_has_tracked_subcontract_components()`, `_check_overprocessed_subcontract_qty()`, `action_show_subcontract_details()`, and a `_compute_show_details_visible()` override.
- `_action_confirm()` no longer raises a `UserError` when no subcontracting BoM is found: the move is simply left as a regular receipt. A `do_not_create_subcontract_order` context key prevents creating a duplicate production when an extra move is added.

**stock.picking**
- `_is_subcontract()` is now based on an incoming picking type plus the presence of a move flagged `is_subcontract`, instead of the partner type.
- Subcontracted production source and destination both use `property_stock_subcontractor`.
- `action_done()` records each production, copies the tracked move lines, and back-dates production moves by one second so they appear before the receipt in traceability.

**stock.move.line**
- `create()` and `write()` now call `_check_overprocessed_subcontract_qty()`.

No method signature removals were detected.

## How your habits should change
- Partners: drop the "Subcontractor" type. Check the new "Subcontractor Location" field instead (Odoo can create it for you). Other partners keep their normal type.
- BoMs: assign subcontractors directly on the BoM, and remember several subcontractors are allowed per BoM.
- Receipts: a missing subcontracting BoM no longer blocks you. You may silently get a standard receipt, so verify your BoM setup.
- For products whose components are tracked, use "Register components" / the Produce wizard instead of typing done quantities by hand, otherwise you get a warning.

## What you gain by migrating
- A simpler, less error-prone subcontracting setup: no partner type, no manual stock-location plumbing.
- Reliable registration of tracked components and serial/lot numbers at the subcontractor.
- Correct move ordering between production and receipt for traceability reports.
- One partner linked to multiple subcontracting BoMs, with a per-company subcontractor location property.
