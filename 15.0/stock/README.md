# stock migration guide (14.0 -> 15.0)

Applies to the Odoo Community `stock` (Inventory) addon: what functional users notice, and what changes in the data model.

## What's new for users

**Inventory adjustments were redesigned.** The old Inventory Adjustment documents (draft / in progress / validated) are gone: you now count directly in a single editable *Stock On Hand* list — count anything, anytime, without creating an adjustment record.

* **Cyclic inventory by location** — locations keep their inventory frequency; a company-level annual inventory month/day covers the rest, and next expected inventory dates are computed.
* **Assign counts to users** — each counted line has "Assigned To" and a scheduled date, so counting work can be dispatched.
* **Suspicious inventory filter** — spot negative stocks and duplicate serial numbers and resolve them with dedicated conflict wizards.
* **History per quantity** — a History button on each line shows moves that affected that product/location.
* **Reservations** — automated, manual, or triggered X days before the scheduled date; mass reservation based on filters.
* **Forecast report** — reserve, unreserve and change priority from the report itself.
* **Removal strategy** — "Closest Location" selectable for a product category.
* **Packagings** — order/sell by packaging, display packaging in warehouse moves, link packagings to package types.
* **Putaway** — rules per package type, storage categories with capacities.
* **Reordering rules / replenishment** — multi-quantity rules with BOM quantities; vendor info visible in replenishment.

## Technical data model changes

**Removed models**
* `stock.inventory` and `stock.inventory.line` are deleted; their functionality moved into `stock.quant`. The related menus and the `stock.move.inventory_id` link go with them.

**stock.quant**
* `inventory_quantity` is now a plain editable "Counted Quantity" (was compute + inverse).
* `inventory_diff_quantity` — new stored "Difference" between counted and on-hand quantity.
* `inventory_date` — new stored "Scheduled Date" (next count date), computed from the location.
* `user_id` — new "Assigned To".
* New methods: `action_view_inventory`, `action_apply_inventory`, `action_inventory_history`, `action_set_inventory_quantity(_to_zero)`, `_apply_inventory`, `_set_view_context`.

**Other models**
* `stock.location`: `last_inventory_date` changed from Datetime to Date; new `_get_next_inventory_date`.
* `res.company`: new `annual_inventory_month` (default December) and `annual_inventory_day` (default 31), exposed in Inventory settings.
* `stock.move`: `inventory_id` replaced by the boolean `is_inventory`; `stock.move.line` gains the related `is_inventory`.
* `stock.move.picking_type_id` is now a stored computed field, always set from the picking. More consistency, but code that fell back to `picking_id.picking_type_id` now reads the move field.
* `stock.warehouse`: removed `warehouse_count` and `show_resupply` (use the multi-warehouse group).
* `stock.picking.type`: removed `rate_picking_late` and `rate_picking_backorders`.
* `stock.picking`: location fields and `description_picking` are now kept in sync with moves through onchange, not only at creation.

## How your habits should change

* No more Inventory Adjustment documents: open *Inventory > Operations > Stock On Hand*, edit counted quantities in the list, apply.
* Counting a subset is done by filtering the quant list instead of configuring a document; counts can be assigned to a user.
* The working columns are counted quantity, difference, scheduled date and assigned user. Per-document adjustment history is replaced by per-quant History.
* Conflict resolution is a wizard triggered when applying a count, not an auto-generated inventory.
* Scheduled counts come from the company annual month/day plus per-location frequency.

## What you gain by migrating

* One simple, modern counting workflow with less setup, fewer records and faster counts.
* Built-in cyclic/annual planning, per-quant history, automatic conflict detection and resolution.
* Better move consistency (`picking_type_id` always set) and smoother location/description handling on pickings.
* Access to 15.0 reservation modes, forecast-report actions, packaging and putaway improvements.
