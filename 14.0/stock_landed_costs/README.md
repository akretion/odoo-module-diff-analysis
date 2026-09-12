# stock_landed_costs migration guide (13.0 -> 14.0)

## What's new for users

The Odoo 14.0 release notes only touch this addon in one line, under Manufacturing: *"Apply landed costs on manufacturing orders."* That is exactly what changed.

- **A landed cost can now target manufacturing orders, not only transfers.** A new **Apply On** field appears at the top of the landed cost form. With only the Inventory app installed, the only choice is *Transfers* and behaviour is identical to 13.0. When the Manufacturing app is installed (through the `stock_landed_costs_mrp` bridge, part of the Community Manufacturing app), *Manufacturing Orders* become selectable.
- **Real money fields.** Total, Cost, Original Value, Additional Landed Cost and New Value are now monetary fields: they display with the company currency and follow currency rounding instead of being plain numbers.
- **Clearer error messages.** Warnings now name the kind of document you selected ("Please define Transfers/Manufacturing Orders on which those additional costs should apply") instead of always talking about transfers.

Everything else in the release notes (Fleet, PoS, Project, other Manufacturing items) is unrelated to this addon.

## Technical data model changes

Added fields:

- `stock.landed.cost.target_model` — Selection, required, default `picking`, `copy=False`, readonly when done. Only the `picking` option is declared in `stock_landed_costs`; the MRP bridge adds the manufacturing option dynamically.

Changed field types (Float -> Monetary):

- `stock.landed.cost.amount_total` (Total)
- `stock.landed.cost.lines.price_unit` (Cost)
- `stock.valuation.adjustment.lines.former_cost`, `additional_landed_cost`, `final_cost`

New related field:

- `stock.landed.cost.lines.currency_id` (related to `cost_id.currency_id`). Valuation adjustment lines keep their existing related `currency_id` (company currency).

Renames without data impact:

- Python classes `LandedCost` -> `StockLandedCost` and `LandedCostLine` -> `StockLandedCostLine`. Model and table names are unchanged, so **no data migration is needed**.

Logic and method changes:

- New onchange `_onchange_target_model`: clears `picking_ids` when the target is not a transfer.
- New helper `_get_targeted_move_ids()` (returns the moves the cost applies to) and `_check_can_validate()`; `button_validate()` now relies on them instead of hard-coding transfers.
- `get_valuation_lines()` now calls `ensure_one()` and reads targeted moves instead of `picking_ids.move_lines`.
- `_check_sum()` was moved but its logic is unchanged.
- Views were updated to display the currency on the new monetary fields.

## How your habits should change

- On a draft landed cost, choose **Apply On** first. If you switch to Manufacturing Orders, the Transfers list is automatically cleared — nothing is silently kept.
- Validation now complains about "the chosen documents" being empty rather than specifically about transfers. Same rule as before: only draft landed costs can be validated.
- Costs and totals are entered and read in company currency with its symbol; the Total still uses zero decimals.
- Existing landed costs are untouched and open normally; on transferred records the field is preset to *Transfers*.

## What you gain by migrating

- One landed cost mechanism for stock transfers **and** manufacturing orders: cost customisation, freight or duty can now be spread over an MO's finished products.
- Consistent currency handling on every amount, on cost lines and on valuation adjustments.
- A future-proof design: the target document is now data-driven, so the mechanism extends beyond transfers.
- Zero data migration for existing landed costs — the change is additive at database level.

*Sizing note: this is a small, low-risk addon (one file, ~10 KB of code changes). The main effort is functional testing of your landed cost scenarios, especially if you also use Manufacturing.*
