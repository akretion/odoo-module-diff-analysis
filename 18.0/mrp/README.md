# mrp migration guide (17.0 -> 18.0)

## What's new for users

The 18.0 release notes extract available to us covers eCommerce only and says nothing about Manufacturing. The items below therefore come from the code changes and apply to the Community edition — no Enterprise-only feature is presented as included.

- **Product catalog in BoMs and MOs** — add components and byproducts to a manufacturing order, and components to a bill of materials, from the same catalog side panel used in Sales and Purchase, with filters (product used in a BoM/MO, kit).
- **Manual consumption is now a choice** — tracked (lot/serial) components are no longer forced to manual consumption; you decide per BoM line.
- **Late manufacturing orders** — delayed indicator, filter and coloured list rows highlight MOs behind schedule.
- **Mass produce serials** — batch production of serialised products is one "Mass Produce" action instead of a wizard.
- **Work centre planning** — backward scheduling, planning simulation in the BoM overview, and a clickable weekly load graph on the work centre overview.
- **Costs** — the MO overview shows real operation cost and WIP accounting now takes the date into account.
- **Documents** — production documents become product documents and appear in the BoM and MO chatters.
- **Smaller wins** — start/pause a MO or many work orders at once, reshuffle operations, cancellation warnings, BoM shown in replenishment, smart button from a transfer to its MOs, rebuilt scrap and unbuild forms.

## Technical data model changes

**Model removed:** `mrp.document` — merged into `product.document`.

**Fields removed:** `mrp.bom.line.manual_consumption_readonly`; `stock.picking.type.use_auto_consume_components_lots` and its related field on `mrp.production`.

**Fields added:** `product.document.attached_on_mrp` (Hidden / Bill of Materials); `mrp.production.is_delayed` (computed and searchable); `stock.picking.mrp_production_ids`; BoM support on `stock.replenish.mixin`; computed/searched `product_is_in_bom`, `product_is_in_mo` and `is_kits` on products.

**Behaviour changes:**
- `mrp.bom.line.manual_consumption` is a plain stored boolean, no longer computed from tracking or operation.
- Manual consumption of a stock move now derives only from its BoM line.
- A document added on a BoM is re-pointed to the product/template with `attached_on_mrp = 'bom'`.
- `mrp.unbuild` and `stock.scrap` onchanges became computed fields — integrations relying on onchange triggers must be reviewed.
- `mrp.document.copy`/`unlink` and `mrp.routing.workcenter._get_comparison_values` are gone.

**Method signature changes** (only matters if you have custom code):
- `mrp.bom.explode(..., never_attribute_values=False)`
- `mrp.bom.line._skip_bom_line(product, never_attribute_values=False)`
- `mrp.production._get_move_raw_values(product, ...)` (was `product_id`)
- `mrp.production._set_qty_producing(pick_manual_consumption_moves=True)`
- `mrp.workorder.button_start(raise_on_invalid_state=False)` and `_cal_cost(date=False)`
- `mrp.workcenter._get_first_available_slot(start, duration, forward=True, leaves_to_ignore=False, extra_leaves_slots=[])`
- `mrp.workorder._read_group_workcenter_id(workcenters, domain)` — `order` argument dropped

## How your habits should change

- **BoM lines:** the Manual Consumption checkbox is unlocked for tracked components. Review your BoMs: tick it where operators must register consumption by hand, leave it clear to speed up validation.
- **Picking types:** "Consume Reserved Lots/Serial Numbers automatically" is gone; that behaviour is now set per BoM line.
- **Documents:** file drawings and specifications on the product, choosing "MRP: Visible at = Bill of Materials", instead of uploading them on the MO or BoM.
- **Serials:** use the Mass Produce action instead of the old serial wizard.
- **Scrap/unbuild:** these forms now fill in automatically; no manual touch is needed to get default locations, BoM or lot.

## What you gain by migrating

Moving to 18.0 gives your production team a faster daily flow (catalog picking, mass actions, automatic form defaults), better visibility (delayed MOs, work centre load graph, interactive overview graphs, real costs, WIP by date) and a cleaner data model (one document model instead of two, manual consumption under your control). Customisations are lighter too: several onchange-driven flows became computed fields, which are easier to extend. Because `mrp.document` and two picking-type fields disappear and several method signatures change, plan the upgrade with a test pass on your BoMs, tracked components and any custom module touching MRP.
