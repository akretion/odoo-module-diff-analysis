# mrp_account migration guide (17.0 -> 18.0)

## What's new for users

The 18.0 release notes for Manufacturing focus on the Master Production Schedule, mass production of serial numbers, Work in Progress entries and the BoM Overview. Those are Enterprise features and are **not** part of this Community addon, so nothing from the official release notes applies directly to `mrp_account`.

What actually changes for you comes from the core analytic rework shipped in 18.0: manufacturing costs are now tracked through the **Project** of the manufacturing order and of the bill of materials instead of a manually maintained analytical distribution. In addition, unbuilding a manufactured product now generates an automatic correction entry when the unbuild value differs from the original build cost.

## Technical data model changes

Analytic distribution removed / replaced:

- `mrp.bom`: the dedicated file is deleted. `analytic_distribution_text`, `analytic_distribution`, `analytic_account_ids` and the `analytic.mixin` inheritance are gone. A project field (added in `mrp`) serves as the default for MOs.
- `mrp.production`: no longer inherits `analytic.mixin`. `analytic_account_ids`, `_compute_analytic_distribution` and the `_check_analytic` constraint are removed, as is `action_view_analytic_accounts()`. `extra_cost` and `show_valuation` remain. Writing `analytic_distribution` on a non-draft MO no longer triggers analytic/valuation recomputation.
- `mrp.workorder._create_or_update_analytic_entry()` was rewritten and now only produces work-center analytic lines, through the new helper `_create_or_update_analytic_entry_for_record(value, hours)`.
- `stock.move._get_analytic_distribution()` override removed: analytic values now follow the standard project-based logic. `_ignore_automatic_valuation()` now combines `super()` with the production check.
- `stock.rule` (`_prepare_mo_vals`) override file removed: MO analytic values are no longer pushed from procurement rules.

Unbuild valuation:

- The `stock.valuation.layer` override file is removed (no more `_candidate_sort_key`), and `stock.move._get_out_svl_vals()` no longer matches the OUT layer of an unbuild to the original MO layer.
- `_create_out_svl()` now compares the unbuild cost with the original build-time unit cost and, for products valued in real time, creates and posts an `account.move` referenced `<unbuild> - Unbuild Cost Difference`, between the stock valuation and production accounts.

## How your habits should change

- Stop filling an analytical distribution on BOMs and manufacturing orders. Set the Project on the BOM (used as default for its MOs) and on the MO; costs are allocated using that project's analytic accounts.
- Work-center cost lines are still generated, but no longer from the MO's own distribution. Expect project-level analytic entries to carry most manufacturing costs.
- Don't chase unbuild valuation imbalances: 18.0 posts a corrective journal entry automatically instead of trying to match the original production valuation layer. Review those entries in Accounting when an unbuild shows a cost difference.
- The BoM Overview availability and Work-in-Progress reporting mentioned in the release notes are Enterprise features; if you handled this with a spreadsheet workaround in 17.0, keep that plan in place.

## What you gain by migrating

- Simpler cost tracking: a single Project drives analytic allocation for BOMs, MOs and their components, with far fewer distributions to maintain.
- Fewer manual steps and fewer mistakes: no analytical distribution to copy from BOM to MO, and BOM project defaults flow down automatically.
- Reliable inventory valuation after unbuilds: automatic correction entries keep product costs and valuation layers consistent with real average/FIFO cost.
- Consistency with the 18.0 project and analytic architecture across sales, purchase, stock and manufacturing, so manufacturing profitability reporting matches the rest of your Odoo.
