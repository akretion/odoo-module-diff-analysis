# repair migration guide (19.0 -> 20.0)

## What's new for users

Odoo 20.0 changes three things you will notice in the Repairs app:

- **Repair order status simplified.** The "Under Repair" status has been removed from repair orders. One less state to monitor, and a shorter path from reception to delivery.
- **Invoice directly from a repair order.** When no quotation is needed, the customer invoice can be created straight from the repair order instead of going through a quotation and a sales order first (uses the standard Sales/Invoicing apps).
- **Services on repair orders.** You can add services (for example labour) to a repair order alongside the parts you already manage.

## Technical data model changes

These come from the 20.0 source changes of the module:

- **Removed: inheritance of `stock.traceability.report`.** The file `addons/repair/models/stock_traceability.py` is deleted and its import removed from `models/__init__.py`. The repair module no longer patches the traceability report.
- **Removed overrides:**
  - `_get_reference(move_line)`: in 19.0 it displayed the repair order (model `repair.order`, its name) as the reference of a traceability line when the move came from a repair. It is gone; the reference now follows the standard stock logic.
  - `_get_linked_move_lines(move_line)`: in 19.0 it used the repair's consumed lines (`consume_line_ids`) as parent lines and produced lines (`produce_line_ids`) as children lines. The refactored stock report now computes these links itself.
- **Stock traceability refactor (the reason for the above).** The report resolves all parent (upstream) and child (downstream) lines in Python and returns the whole result in a single call to the interface instead of one call per unfold/fold. A chain is explored in one report, and the most recent lines are shown at the top.
- No model or field is added by these changes.

## How your habits should change

- **Traceability reference:** a line coming from a repair no longer shows the repair order as its reference; you now see the standard stock reference. Identify the repair through the move chain.
- **One report instead of two:** no more jumping from one report to the next to follow a flow. Upstream and downstream are available in the same view, so a repair can be read from the components consumed to the product delivered.
- **Reading order:** the newest lines appear first, so reading a long chain is different from 19.0.
- **Status:** stop filtering, grouping or automating on the removed "Under Repair" state. Update saved filters, dashboards, automated actions, server actions and custom reports that reference it.
- **Invoicing:** if your habit was to always create a quotation before invoicing, check whether you can now invoice the repair order directly when no quotation is required.

## What you gain by migrating

- A simpler repair status flow and direct invoicing: fewer clicks and fewer documents to follow for the same job.
- Services sold on the same repair order as the parts you repair.
- One traceability report for the whole chain, faster (one server round-trip instead of many), with the latest activity first.
- A cleaner technical base: with the repair-specific patch of the stock report removed, there is less custom code and less upgrade friction in future versions.
- Anything requiring Enterprise apps (for example Field Service) is out of scope of this Community migration; ask Akretion if you need it.
