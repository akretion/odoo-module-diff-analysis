# stock migration guide (19.0 -> 20.0)

## What's new for users

Odoo's official 20.0 release notes do not cover this addon; the changes below come from the 19.0 → 20.0 code diff of the `stock` module itself.

- **Scrap is now a stock move.** The dedicated Scrap document is gone. The Scrap menu lists the scrap moves themselves, with the fields you know (product, quantity, lot/serial, source and scrap location, reason tags, replenish). You can also scrap a lot/serial number directly from its form.
- **Scrap reason tags** and **Should Replenish** are now options carried by the move.
- **Allocation report replaces the Reception Report.** There is no global setting left: display, auto-print and label options are configured per operation type (never for outgoing ones).
- **Returns and exchanges are actions on the transfer** (return all, return a selection, exchange) instead of a separate wizard.
- **Put in Pack from Detailed Operations**, including splitting lines by package capacity.
- **Reordering rules**: a new wizard mass-updates the demand basis and factor, recomputing daily demand, min and max.
- **Inventory adjustments can be backdated** when the date is editable.
- **Settings clean-up**: individual shipping-connector checkboxes disappeared; delivery methods are opened from a single "Delivery Methods" view.

## Technical data model changes

- **Removed model**: `stock.scrap`, plus `scrap_id` on `stock.move` and `stock.move.line`.
- **`stock.move`**: added `is_scrap`, `scrap_reason_tag_ids`, `should_replenish_scrapped`; `reference` is now writable; `lot_ids` restricted to the move's product. `stock.lot` gains `is_scrap`.
- **`res.company`**: added `scrap_location_id`, `default_stock_location_id`.
- **Moved to `product`**: `virtual_available`, `incoming_qty`, `outgoing_qty`.
- **Renamed**: orderpoint `based_on` → `min_max_based_on`, `percent_factor` → `min_max_based_on_factor`; UoM logistic fields (`product_uom` → `uom_id`).
- **Removed fields**: `show_operations` (picking, operation type, move), `package_type_sequence_id`, and the `group_stock_reception_report` setting.
- **Picking batch** now lives in `stock` (`stock.picking.batch`), with batch/wave merging and automatic batch/wave creation options on operation types.
- **Signatures to audit in custom modules**: `stock.rule._get_push_rule(move, location_dest_id, route_ids, warehouse_id, extra_domain)` (`_run_push` removed), `_get_stock_move_values(..., uom_id, ...)`, `_get_lead_days(..., bypass_delay_description=...)`, `stock.quant.action_apply_inventory(date=None)`, `stock.picking._create_backorder(..., from_manual_backorder=False)`, `stock.move.line.action_put_in_pack(..., package_capacity=None)`, `stock.warehouse.orderpoint.action_replenish()` (no more `force_to_max`).

## How your habits should change

- Scrapping happens in the Scrap list of stock moves, or from a transfer/lot; there is no scrap order to consult afterwards. Scrapping a kit is no longer possible.
- If you relied on the global Reception Report setting or company-level auto-print flags, re-enable them per operation type.
- Returns and exchanges are buttons on the transfer; no return wizard to fill in.
- Reports, filters or imports based on `stock.scrap`, or on the stock-owned `virtual_available`, must be adapted.
- Customisations using `show_operations`, `based_on`, `percent_factor`, `product_uom`/`product_uom_id` or the reception-report group need updating.

## What you gain by migrating

- One consistent document — the stock move — for scrap, inventory and transfers: fewer records, full traceability.
- Scrap, returns and put-in-pack available where the work happens, with fewer dialogs.
- Allocation and label behaviour configurable per operation type instead of a single company-wide switch.
- On hand, incoming, outgoing and forecasted quantities available at product level, preparing lighter inventory setups.
- Easier reordering-rule tuning thanks to the demand suggestion wizard.
- A supported release whose data model converges with upcoming versions, lowering the cost of future upgrades.
