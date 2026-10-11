# project migration guide (17.0 -> 18.0)

## What's new for users

**Analytic plans on projects.** A project is no longer limited to a single analytic account: you can now attach one account per analytic plan (project, department, staff, customer, ...) and read profitability at a much finer level. Costs and revenues coming from stock pickings and landed costs are captured too, and delivered products can be re-invoiced to the customer automatically instead of being added by hand to the sales order.

**Navigation from the control panel (top bar).** Related records (sales orders, purchase orders, manufacturing orders, BoMs, stock transfers, analytic items) can be browsed directly from the project and task views.

**"Project Updates" is renamed "Dashboard"** in the project views.

Other Project highlights of the 18.0 release notes (Gantt progress bar, burn-up chart, batch scheduling with deadlines, task revisions, Gantt deadline display) rely on Enterprise features and are **not** part of this community addon.

## Technical data model changes

**project.project**
- `analytic_account_id` removed. Replaced by `account_id` (one account per plan), enabled by the new inheritance of `analytic.plan.fields.mixin`; `analytic_account_balance` now follows `account_id`.
- New project-side support for a "sales order" used for re-invoicing stock moves.
- `_get_values_analytic_account_batch` now takes a list of value dicts; the analytic plan used for projects is resolved automatically instead of coming from a configuration parameter.
- Deprecated fields removed: `doc_count`, `allow_rating`.
- Project company is now derived from `account_id`.

**project.task**
- `analytic_account_id` removed, along with `_compute_analytic_account_id()` and `_get_task_analytic_account_id()`.
- Task creation no longer copies the project's analytic account.

**account.analytic.account**
- `project_ids` now points to `project.project.account_id`.

**project.task.type**
- Deprecated `description` field removed.

**res.config.settings**
- `analytic_plan_id` (system parameter `analytic.analytic_plan_projects`) removed.

**Around the addon (sale, purchase, stock, mrp)**
- The analytic account/distribution on sales orders, purchase orders, manufacturing orders and BoMs is replaced by a **project** field; the project's accounts become the default distribution of document lines.
- Stock operation types get an "analytic costs" option; stock pickings get a project field, generate analytic entries on validation (negative for deliveries, positive for receipts) and support re-invoicing of delivered products.

## How your habits should change

- Do not set "the" analytic account on a project any more — fill in the new Analytic section (one account per plan). The account on the *Project* plan is still the one feeding the profitability report.
- You can no longer set an analytic account on a task; timesheets take it from the project, or from the sales order line when the task comes from a quotation (the SOL wins).
- Link business documents to a **project** (sales order, purchase order, MO, BoM, stock picking) rather than to an analytic account; distributions are filled in for you.
- Check that your projects are linked to the right sales order if you rely on re-invoicing stock.
- The "Project Updates" entry is now "Dashboard".
- Custom reports, exports or imports using `analytic_account_id` on projects/tasks, `doc_count`, `allow_rating` or stage `description` must be updated.

## What you gain by migrating

- Real multi-dimensional cost and revenue tracking per project, without duplicating projects.
- Materials actually consumed on a job (stock moves, landed costs) finally appear in project profitability, and can be re-invoiced automatically.
- Fewer manual steps and fewer errors: distributions default from the project everywhere.
- A cleaner data model, with obsolete fields (document counter, rating shortcut, stage descriptions) removed.
- A supported upgrade path: these changes are covered by the standard 17.0 → 18.0 upgrade scripts.
