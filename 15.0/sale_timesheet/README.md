# sale_timesheet migration guide (14.0 -> 15.0)

## What's new for users

The official 15.0 release notes say nothing specific to the **billing** behaviour of `sale_timesheet`. The "Timesheets" section only covers adjacent topics (automatic timesheet entries from time off, an overtime overview), and the "Project" section covers tasks, sharing, Gantt, portal and profitability — several of which are Enterprise-only in 15.0. None of them change how Community `sale_timesheet` bills your timesheets, so they are not claimed here.

What really changes for the user is **how a billable project is configured**, described below.

## Technical data model changes

**1. Project pricing: two fields become one**

- `project.project.bill_type` (*Different customers* / *A unique customer*) is **removed**.
- `project.task.bill_type` (related to the project) is **removed**.
- `project.project.pricing_type` now carries the three former combinations:
  - `task_rate` — "Task rate" (previously `bill_type = customer_task`)
  - `fixed_rate` — "Project rate"
  - `employee_rate` — "Employee rate"
- The default of `pricing_type` changes from `fixed_rate` to `task_rate`.

**2. The `non_allow_billable` flag disappears**

- `project.task.non_allow_billable` and `account.analytic.line.non_allow_billable` are **removed**.
- The `timesheet_invoice_type` selection value `non_billable_timesheet` is removed from `account.analytic.line`.
- The billable nature of a timesheet is now derived only from the project/task pricing type and the sales order line: there is no per-task "do not bill these timesheets" switch anymore.

**3. Employee-rate mapping (`project.sale.line.employee.map`)**

- `timesheet_product_id` is **removed** from the mapping; that field only makes sense for field-service projects and now lives in the FSM sale module. `price_unit` is computed solely from the related sales order line.

**4. Behavioural and API changes**

- `AccountAnalyticLine._timesheet_determine_sale_line()` changes signature: it was an `@api.model` method taking `(task, employee, project)`, it is now a recordset method with no argument.
- `ProjectTask.write()` no longer clears `sale_line_id` when a task is moved to an employee-rate project.
- `ProjectTask._get_timesheet()` now relies on `_is_not_billed()`.
- `SaleOrderLine.name_get()` supports a new `with_price_unit` context key (used to disambiguate several SO lines of the same product by unit price).
- The Project Overview now filters on the `active_ids` of the context.

## How your habits should change

- Instead of choosing first "Invoice tasks to" and then "Pricing", you now select a single **Pricing** value on the project: Task rate, Project rate or Employee rate.
- New projects default to **Task rate**. In 14.0 the default was Project rate, so review the configuration of your existing projects after migration.
- There is no longer a "Non-Billable" checkbox on tasks or timesheets: exclude time from billing through the project/task configuration instead.
- Employee-rate mappings no longer hold a "Service" product; the price always comes from the sales order line.
- Moving a task to an employee-rate project keeps its sales order line instead of resetting it.

## What you gain by migrating

- Simpler, less error-prone configuration: one pricing field instead of two interacting selections.
- Employee-rate projects — including field-service ones — now correctly pick up each employee's rate from the SO line mapping when the task is done.
- Fewer edge cases where timesheets were silently treated as non-billable, and a more consistent computation of delivered quantities and invoicing.
- Staying on a supported version: 14.0 is out of standard support, while 15.0 keeps receiving fixes on timesheet billing.
