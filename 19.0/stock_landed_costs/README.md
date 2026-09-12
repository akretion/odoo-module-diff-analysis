# stock_landed_costs migration guide (18.0 -> 19.0)

## What's new for users

Landed costs are now built on Odoo 19's rewritten inventory valuation engine. The official release notes only mention this topic once, under Inventory: *"Inventory valuation has been simplified and new features have been added, including a new closing interface and support for transfer backdating."* There is **no dedicated release-notes entry for landed costs** — everything below comes from the code changes shipped with that valuation rewrite.

What a landed cost user actually gets:

- Landed cost amounts are prorated on the quantity **still in stock**, and only that remaining part generates an accounting entry.
- The cost is stored as a value on the stock move (a landed cost value per move) instead of being tracked in separate valuation layers.
- Values can be read "at a date", so reports and closings can reflect landed costs at a given point in time.
- Adding a landed cost product through a catalog/quick-add flow now takes the landed cost into account correctly (19.0 fix).

## Technical data model changes

**Models**
- `stock.valuation.layer` is removed; the addon's inheritance of it (and its `stock_landed_cost_id` field) is gone.
- `stock.landed.cost.stock_valuation_layer_ids` (One2many to `stock.valuation.layer`) is removed.
- `stock.landed.cost.reconcile_landed_cost()` and `action_view_stock_valuation_layers()` are removed.
- `account.move._post()` override removed: posting a vendor bill no longer re-runs landed cost reconciliation. Validation now triggers `_set_value()` on the moves of the adjustment lines.
- New `account.move._update_order_line_info()` override (landed cost from catalog fix).

**stock.move**: removed `_get_stock_valuation_layer_ids()`; added `_get_landed_cost(at_date=None)` and `_get_value_from_account_move(quantity, at_date=None)`.

**account.move.line**: removed `_get_stock_valuation_layers(move)` and `_eligible_for_cogs()`; added `_eligible_for_stock_account()`.

**stock.valuation.adjustment.lines**
- `_create_accounting_entries(remaining_qty)` (was `_create_accounting_entries(move, qty_out)`).
- `_create_account_move_line(credit_account_id, debit_account_id, remaining_qty)` (was `(move, credit_account_id, debit_account_id, qty_out, already_out_account_id)`). The entry amount is prorated by `remaining_qty / quantity`, and **no lines at all** are created when nothing remains in stock.

**Accounts**
- Landed cost lines now default to the product's `expense` account instead of `stock_input`; the vendor-bill journal entry uses `stock_valuation` instead of `stock_input`.
- Input/output accounts no longer exist, so the "already out" counterpart lines — and the dropship special case using the expense account — are gone.
- On valuation adjustment lines, `former_cost` is now taken from the move's value rather than summing valuation layers.

## How your habits should change

- Review product accounting configuration for landed cost products: the **expense** account is now the counterpart and the **stock valuation** account is debited. Stock Input / Stock Output accounts are no longer used.
- Do not expect a reconciliation step between the vendor bill and the landed cost entry; it no longer exists. The value is recomputed from the moves.
- Landed costs only capitalize what is still in stock at validation. Costs allocated to goods already shipped no longer produce dedicated "already out" entries.
- The valuation-layers button/action on a landed cost is gone — use the standard inventory valuation views instead.
- Historical 18.0 valuation layers are not carried over; the 19.0 value is recomputed from accounting documents (bill, order, standard price).

## What you gain by migrating

- Landed costs handled by the new, document-driven valuation engine, consistent with bills and stock moves.
- Value-at-date support, useful for closings and backdated transactions.
- Fewer accounts to configure (no input/output accounts) and no manual reconciliation to babysit.
- A single source of truth for cost of goods — the stock move — with landed costs included.
