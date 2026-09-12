# sale_project migration guide (17.0 -> 18.0)

## What's new for users

**Analytic plans on projects** (the main change). A project is no longer tied to a single analytic account: you can assign analytic accounts on several plans at once, so costs and revenues can be tracked at a finer level (team, department, service, customer) while still keeping the usual project profitability view. Sales orders now have a **Project** field; the analytic accounts of that project feed the default analytic distribution of newly added order lines. Tasks no longer have their own analytic account: timesheets inherit the distribution from their project or from their sales order item.

**Re-invoicing of delivered goods.** A project can reference a sales order used to re-invoice products delivered from stock pickings. The stock / purchase / MRP parts of this feature live in other addons, not in sale_project.

**Project dashboard.** Project Updates are turned into a Dashboard, organized in foldable sections per sales order.

**Smaller improvements:** a customer-preview stat button on the project form; a completed-task percentage shown on task stat buttons; clearer help texts and tooltips on service products (service tracking, invoicing policy); projects generated from a quotation now reuse the milestones of the project template; and a clear blocking message when a product that creates a task in a global project is sold without any project defined on the quotation or on the product.

Other Project highlights of the 18.0 release notes (Gantt enhancements, burn-up chart, allocated hours, project sharing, field service) belong to Enterprise or to other addons and are **not** part of sale_project in Community.

## Technical data model changes

- **Code split per model.** `sale_project/models/` now holds one file per model: `project.py` becomes `project_project.py`, `project_task.py`, `project_task_recurrence.py`; `product.py` becomes `product_product.py` and `product_template.py`. Purely structural (no data change), but custom code importing those files or classes by path must be reviewed.
- **Analytic fields reworked.** On `project.project`, `analytic_account_id` is replaced by `account_id` plus one account per plan in the analytic notebook; the analytic account on tasks is removed. On `sale.order`, `analytic_account_id` is replaced by `project_id` (and related `project_account_id`). `vendor_bill_count` is now related to `account_id`. Invoice counts, stat buttons, revenue and profitability filters now work on `account_id` / `analytic_distribution`.
- **New fields:** `project.project.reinvoiced_sale_order_id`, `sale.order.project_id`, `sale.order.project_account_id`.
- **New default logic:** `sale.order.line._compute_analytic_distribution()` takes the distribution from the product's project, the project template or the sales order's project; `_timesheet_create_project_account_vals()` creates the project's analytic account from the SO. `_handle_milestones()` handles template milestones.
- **Signature changes:** `project.project.get_sale_items_data()` loses its `domain` argument and gains `section_id`; `_get_sale_items()` is removed in favour of `_get_domain_from_section_id()` and `_get_foldable_section()`; `action_project_sharing()` is removed; `_map_tasks_default_valeus` typo fixed to `_map_tasks_default_values(project)`; `project.task._group_expand_sales_order()` no longer takes `order`; `_domain_sale_line_id()` refines the sales order item domain on projects and tasks.
- **New helpers:** `account.move.line._sale_determine_order()`, `_get_so_mapping_from_project()` and `_get_so_mapping_domain()` match a vendor bill line to the sales order to re-invoice through the project's analytic accounts.

## How your habits should change

- Where you set one analytic account on a project, you now fill the analytic accounts per plan; project profitability keeps reading the *Project* plan.
- On a quotation, set the **Project** to drive both task generation and the default analytic distribution of new lines.
- Use the project's *Sales Order* field to declare where delivered stock items should be re-invoiced.
- Service products creating a task in a global project now need a project on the quotation or on the product, otherwise confirmation is blocked with an explicit message.
- Don't look for the analytic account on tasks or timesheets: adjust it on the project or on the sales order item.
- The *Project Updates* menu is now the *Dashboard*.

## What you gain by migrating

- Multi-dimensional project profitability: one project can be analysed across several analytic plans without changing your reporting habits.
- Bottlenecks removed between sales, projects and invoicing: the sales order project now drives task creation and analytic defaults.
