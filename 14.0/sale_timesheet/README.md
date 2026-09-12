# sale_timesheet migration guide (13.0 -> 14.0)

## What's new for users

- **Billable projects are now explicit.** Project billing was reworked: instead of one implicit "Billable Type", each project asks two clear questions — *Customer Type* (invoice each task separately to different customers, or bill all tasks of the project to one customer) and *Pricing* (fixed rate or employee rate). Non-billable projects are simply those where *Billable* is off.
- **Exclude time from billing.** A new *Non-Billable* flag on timesheet entries keeps time out of invoices, and excluded time gets its own "Non Billable Timesheet" category in project reporting.
- **Link with Sales.** You can change the Sales Order Item to which not-yet-invoiced timesheets are linked, and transfer uninvoiced timesheets from one task to another.
- **Generate a project or task from a Sales Order without the Timesheets app** — that capability now lives in its own module.

Other 14.0 release-note items (timer, grid view, barchart footer, portal details) belong to the core Timesheets/Project apps, not to this add-on.

## Technical data model changes

**Module split.** sale_timesheet now depends on the new Community module **sale_project**, which hosts project/task generation from Sales Orders: `service_tracking` on products, `sale_line_id`/`sale_order_id` on projects and tasks, `project_id`/`task_id`/`is_service` on sale order lines, `tasks_ids`, `tasks_count`, `project_ids`, `visible_project`, `action_view_task` and `_timesheet_service_generation`. `_timesheet_create_project` now calls `super()`.

**Billing fields.** `project.project`: `billable_type` (task_rate/employee_rate/no) removed; `bill_type` (`customer_task`/`customer_project`) and `pricing_type` (`fixed_rate`/`employee_rate`) added, stored with defaults; `warning_employee_rate` added; `allow_billable` relabelled "Billable"; turning it off clears `so_line` on uninvoiced timesheets. `project.task`: `billable_type` removed, `bill_type`/`pricing_type` related to the project, new `timesheet_product_id`, `sale_order_id` now computed and stored.

**Timesheets.** `account.analytic.line` gains `non_allow_billable` and a new invoice type `non_billable_timesheet`. Delivered-quantity computation and the SO timesheet total ignore non-billable entries.

**Employee mapping** (`project.sale.line.employee.map`): `sale_line_id` is no longer required; `price_unit`/`currency_id` are computed-editable instead of related; a new `timesheet_product_id` allows a rate without a SO line; creating a mapping line re-links uninvoiced timesheets of that employee.

**Invoicing.** New `account.move._link_timesheets_to_invoice(date=None)`; the old `account.move.line.create()` override is gone. `sale.order._create_invoices(grouped, final, date=None)` accepts a cut-off date, and `_recompute_qty_to_invoice` (a date, later a start/end range) recomputes quantities only from timesheets up to that date.

## How your habits should change

- Review each project after migration: "Billable Type" no longer exists. Choose *Customer Type* and *Pricing*; projects that were "No Billable" should end up with *Billable* unchecked.
- Check the employee-rate mappings. Rows may now exist without a Sales Order Item (with a service product and its own price), and edits relink uninvoiced timesheets automatically.
- Instead of deleting and re-encoding time, use the new ability to change the SO item of uninvoiced timesheets or move them to another task.
- When invoicing a service billed on delivered timesheets, use the new "invoice timesheets up to <date>" field; later timesheets stay available for the next invoice.
- If you generate tasks from Sales Orders, the screens are unchanged, but the feature now comes from sale_project — make sure that module is installed.

## What you gain by migrating

- A clearer, more explicit billing setup, configured per project.
- Finer invoicing control thanks to the cut-off date, avoiding over-invoicing.
- Cleaner reporting: non-billable time is separated instead of polluting billable totals.
- Far fewer manual fixes when timesheets must be re-linked to another order item or task.
- A lighter dependency for teams that create tasks from Sales Orders without tracking time.
- The standard upgrade path converts existing settings, mappings and timesheet links, so nothing has to be re-encoded.
