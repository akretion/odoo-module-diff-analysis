# stock_landed_costs migration guide (18.0 -> 19.0)

## What's new for users

Odoo 19.0 rebuilt inventory valuation. The official release notes only mention it briefly under **Inventory**: *"Inventory valuation has been simplified and new features have been added, including a new closing interface and support for transfer backdating."* Landed costs are not named, so the points below come from the Community code itself (`stock_account` / `stock_landed_costs`).

Unchanged: the landed cost form, the split methods (equal, by quantity, by weight, by volume, by current cost) and the *Compute Landed Cost* button work as before.

Changed when you validate and post a landed cost:

- **Valuation layers are gone.** No more `stock.valuation.layer` records, and no "Stock Valuation Layers" list to open from a landed cost.
- **The cost now sits on the receipt's stock move**, added on top of the move value, instead of creating extra layers.
- **No more "already out of stock" entries.** Odoo 18 posted extra lines for goods already delivered or sold (stock output account, plus a COGS entry in anglo-saxon mode). Odoo 19 only adjusts what is still in stock; the rest is handled by the new closing interface.
- **No more automatic reconciliation** of the vendor bill against the stock input account when the landed cost is posted.
- **Standard price, FIFO and AVCO are no longer adjusted** by a landed cost.

## Technical data model changes

Removed:
- `stock.valuation.layer` inheritance (`stock_valuation_layer.py` deleted) and its `stock_landed_cost_id` field.
- `stock.landed.cost.stock_valuation_layer_ids` (One2many).
- Methods `action_view_stock_valuation_layers()` and `reconcile_landed_cost()`.
- The `account.move._post()` override that called `reconcile_landed_cost()`.
- The `account.move.line._get_stock_valuation_layers()` override.

Changed:
- `_eligible_for_cogs()` renamed to `_eligible_for_stock_account()`.
- Landed cost lines created on the vendor bill now use the **stock valuation** account (was **stock input**).
- The default account of a landed cost line is now the **expense** account (was **stock input**).
- `_create_accounting_entries(move, qty_out)` becomes `_create_accounting_entries(remaining_qty)`.
- `_create_account_move_line(move, credit, debit, qty_out, already_out_account)` becomes `_create_account_move_line(credit, debit, remaining_qty)`: it returns no line when nothing remains in stock, and prorates the amount by `remaining_qty / quantity`.
- `stock.move._get_stock_valuation_layer_ids()` is replaced by `_get_landed_cost(at_date=None)`; `_get_value_from_account_move()` is overridden to add the landed cost value to the move.
- `get_valuation_lines()` reads `former_cost` from `move._get_value()[0]` instead of summing layers.
- `button_validate()` posts the accounting entry (real-time products only) and calls `_set_value()` on the related moves.

## How your habits should change

- Stop auditing landed costs through valuation layers; check the receipt's stock move value instead.
- Reconciliation with the stock input account is no longer automatic, and input/output accounts no longer exist as a concept in the new valuation engine. Review your landed cost accounting configuration before going live.
- Update the accounts set on your landed cost products and lines: defaults moved to the expense account, and the vendor bill line now targets the stock valuation account. Expect different (and fewer) journal items on validation.
- Adapt any report or customization reading `stock.valuation.layer` or `stock_landed_cost_id`.
- Test on a copy of your database: validate landed costs on partially delivered receipts and compare the entries with 18.0. Use the new closing interface and back-dating support to handle goods already delivered but not yet invoiced.

## What you gain by migrating

- A simpler valuation model: one value per stock move, justified by actual documents rather than a long chain of layers.
- A dedicated closing interface that surfaces delivered-not-invoiced and to-receive items, so accruals are visible instead of hidden in layers.
- Back-dated transfers are supported, so correcting an earlier receipt no longer means replaying history.
- Fewer input/output accounts to configure and fewer manual reconciliation steps.
- Landed costs fit the new model natively: the cost is reflected in the move value at a given date, which makes reporting and audits easier.
