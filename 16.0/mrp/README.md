# mrp migration guide (15.0 -> 16.0)

Odoo 16.0 reworks how Manufacturing Orders schedule work orders: the former strictly sequential chain ("next work order") is replaced by an explicit dependency graph between operations.

## What's new for users

The official 15.0 → 16.0 release notes extract available for this work contains no section about the `mrp` addon (its content is Enterprise-oriented), so the changes below are drawn from the actual code deltas.

* **New setting "Work Order Dependencies".** In Manufacturing ▸ Settings, a toggle enables dependency management between operations. It is off by default.
* **Dependencies defined on the Bill of Materials.** Each BoM gets an "Operation Dependencies" flag. When it is ticked, each operation form shows which operations must be finished before it can start ("Blocked By"), and which ones it blocks. Cyclic dependencies are refused with a clear error.
* **Parallel and mixed scheduling.** Work orders with no predecessor start first; several can run at the same time. Successors start only once all their predecessors are finished, step by step.
* **Gantt view.** The work order Gantt view lets you visualise and edit these dependencies.
* **Safe default.** If the BoM flag is not ticked, Odoo still creates the dependencies needed to reproduce the old strictly sequential behaviour. Disabling the setting does not disturb productions already planned.

## Technical data model changes

Added fields:
- `mrp.bom.allow_operation_dependencies` (Boolean, "Operation Dependencies").
- `mrp.production.allow_workorder_dependencies` (Boolean) and helper method `_link_workorders_and_moves()`.
- `mrp.routing.workcenter`: `allow_operation_dependencies` (related to the BoM), `blocked_by_operation_ids` and `needed_by_operation_ids` (Many2many via `mrp_routing_workcenter_dependencies_rel`).
- `mrp.workorder`: `blocked_by_workorder_ids`, `needed_by_workorder_ids` (Many2many via `mrp_workorder_dependencies_rel`), `is_planned` (related).
- `res.config.settings.group_mrp_workorder_dependencies`.

Removed field:
- `mrp.workorder.next_work_order_id` (Many2one) — replaced by the dependency pair above.

Changed behaviour and signatures:
- `mrp.workorder._compute_state` now depends on `blocked_by_workorder_ids` and their states: a work order stays `pending` until every predecessor is `done`/`cancel`, and goes back to `pending` if that is no longer true. The state field is now `recursive=True`.
- `mrp.production._plan_workorders()` no longer accepts the `replan` argument. It links work orders and moves, then plans the final work orders by calling the new `mrp.workorder._plan_workorder(replan=False)`, which schedules predecessors first. MO planned start/finish dates are now the minimum/maximum of the work order calendar slots instead of the first/last work order dates.
- `_start_nextworkorder()` (mrp.workorder) has been removed.
- `copy()` on `mrp.bom` and `mrp.production` now re-links the dependency graph; `_action_confirm()` delegates the linking to the production.
- New cycle checks on operations and work orders; backorder code simplified (state is derived from dependencies rather than forced).

## How your habits should change

- Do nothing and you keep 15.0 behaviour: sequential work orders, same planning logic, same statuses.
- To use the new capability, enable "Work Order Dependencies" in the settings (once), then tick "Operation Dependencies" on the relevant BoMs and declare dependencies per operation.
- Tick the BoM flag and declare nothing, and Odoo assumes all operations may start simultaneously — a real scheduling change, so use it deliberately.
- On each work order you now read "Blocked By" / "Blocks" instead of "Next Work Order". Any saved filter, export, report or customisation referencing `next_work_order_id` must be updated.
- Planning a MO now also re-links operations and moves; dates planned are computed from the whole set of slots, so a MO's start date may be earlier than before when operations run in parallel.

## What you gain by migrating

- Realistic planning: schedule operations in parallel or in a dependency graph that matches your actual shop floor, instead of an artificial one-after-the-other chain.
- Clearer, self-documenting BoMs and a Gantt view where dependencies are visible and editable.
- Fewer stuck work orders: readiness is computed automatically from predecessors, including on backorders.
- A more robust model (cycle detection, consistent copy of dependencies) and reduced risk of corrupted sequences after replanning.
- Standard Odoo 16.0 code, keeping you on a supported, upgradeable branch for future versions.
