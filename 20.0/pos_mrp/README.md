# pos_mrp migration guide (19.0 -> 20.0)

> Scope note: for this addon, no Odoo 20.0 release-note extract matched `pos_mrp`. Everything below therefore comes from the code deltas (data model impacting commits and method signature changes) found between 19.0 and 20.0.

## What's new for users

The only user-visible topic touched by this addon is the **cost of kit (phantom BOM) products sold through the Point of Sale**, and the **Cost of Goods Sold (COGS) entries** posted afterwards.

- Kit costing in the POS is no longer computed by `pos_mrp` itself. It is now handled by the same shared costing logic used by Sales and Inventory (stock_account), which computes a **price unit per sale order line**.
- Consequence with FIFO costing: before, each delivery/invoice pair kept the COGS of the stock layer it consumed (e.g. 10 then 20 in the core example). Now a single price unit is evaluated for the whole order line (e.g. 15) and the linked COGS lines are **re-evaluated to that value**.
- Accounting therefore no longer keeps a series of volatile, move-by-move COGS values for the same product: entries can be replayed/updated when a stock move's value changes.

Because the release notes did not cover this addon, other minor user-facing changes may exist; only the ones above are confirmed by the code diffs.

## Technical data model changes

Source: pseudo patches only (del:1, add:0, no field added).

Removed:
- **`stock.move` inheritance removed.** `addons/pos_mrp/models/stock_move.py` was deleted and `stock_move` removed from `models/__init__.py`. `pos_mrp` no longer extends `stock.move` at all.
- **`StockMove._get_price_unit()` override removed.** It read the `pos_order_line_id` and `bom_id` context keys and returned `_get_kit_price_unit(...)` for kits.
- **`PosOrderLine._get_product_cost_with_moves(moves)` override removed.** It looked up the phantom BOM and called `moves._get_kit_price_unit(product, bom, qty)`; it now falls back to the core implementation.
- **Context propagation removed.** The `stock_moves.filtered(...)` call in `pos_order_line` no longer chains `.with_context(pos_order_line_id=..., bom_id=...)`.

Unchanged: the helper that filters the stock moves of a kit (collecting BOM components / BOM line ids) keeps its logic; `pos_order`, `pos_order_line` and `stock_picking` are still imported.

Net effect: the addon is now nearly empty. Kit price-unit and COGS replay logic lives in core (`stock_account` / `stock.move` revaluation — odoo/odoo#276163, with `odoo/upgrade` scripts referenced in the PR).

## How your habits should change

- **Frozen COGS no longer guaranteed.** If you relied on a POS kit's COGS never changing after posting, expect COGS lines to be recomputed when the related move values change, with one price unit per sale order line.
- **Margin analysis.** Margins on POS kit sales can move after later receipts at different costs; review margins on a "re-read" basis rather than as frozen at invoice date.
- **Customizations / Studio.** Any custom code calling `_get_price_unit` with `pos_order_line_id`/`bom_id` context, or overriding `_get_product_cost_with_moves` from `pos_mrp`, must be reworked: those hooks no longer exist in this addon.
- **Data migration.** No new model or field: no column-level migration needed for this addon. The data-heavy part is in core (stock_account upgrade scripts).

## What you gain by migrating

- A much thinner addon: fewer overrides means fewer conflicts with your other customizations and less breakage on future upgrades.
- One single costing/COGS logic for kits across POS, Sales and Inventory, reducing discrepancies between POS margins and accounting.
- COGS entries that are recomputable and correctable: a single global price unit per order line instead of volatile, per-move values.
- You stay on a supported version and benefit from the related core accounting/stock fixes shipped with the 20.0 revaluation work.
