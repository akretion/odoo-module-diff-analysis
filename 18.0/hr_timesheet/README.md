# hr_timesheet migration guide (17.0 -> 18.0)

## What's new for users

**Analytic plans on projects — the headline change.** A project no longer has a single "Analytic Account". In Odoo 18 you define **one analytic account per analytic plan** (Project, Department, Customer, …) in the project form. A timesheet can therefore post its cost to several accounts at once, so project profitability can be read on several axes instead of one. Timesheets inherit the accounts configured on their project; when a timesheet is linked to a sales order line, the distribution of that line is used instead (the SO line has priority).

**Timesheets as an analytic applicability domain.** "Timesheet" now appears in the analytic plan applicability settings, so a plan can be made mandatory for timesheet entries. If the project lacks a required plan, Odoo blocks the entry and names the missing plan instead of silently mis-posting.

**Timesheet grid view.** Weekends can be shown or hidden in the grid, and under-assigned hours are easier to spot.

**Related records from the task kanban.** The task kanban control panel gains embedded actions to jump to related records, including timesheets.

The remaining release-note items (burn-up chart, Gantt workload and deadlines, revision tracking, …) belong to the Project/Enterprise scope and are not part of this addon.

## Technical data model changes

- `project.project.analytic_account_id` is **renamed to `account_id`** (same role). `analytic_account_active` still exists and is now related to `account_id.active`.
- `project.task.analytic_account_id` is **removed**: tasks no longer carry their own analytic account. `analytic_account_active` becomes a related field on `project_id.analytic_account_active`, and `_compute_analytic_account_active` disappears.
- Timesheet value preparation: `_timesheet_preprocess(vals_list)` is **removed**, replaced by **`_timesheet_preprocess_get_accounts(vals)`**, which fills one field per analytic plan from the project and validates that the mandatory plans are set.
- New `account.analytic.applicability` extension: `business_domain` gains the `timesheet` value (cascade on delete).
- New methods on `account.analytic.line`: `_ensure_uom_hours` (installing timesheets without a UoM no longer tracebacks), `_is_updatable_timesheet` (blocks editing a timesheet tied to a validated sales order line), `_get_favorite_project_id_domain` (favourite-project suggestion ignores global time off), `_compute_message_partner_ids` / `_search_message_partner_ids` (faster portal access to timesheets), `get_views` (keeps the WIP report out of timesheet views).
- New methods on `project.project`: `action_view_tasks`, `get_create_edit_project_ids`; on `project.task`: `_compute_allow_timesheets`, `_search_allow_timesheets`, `_get_timesheet_report_data`.
- View and label plumbing: `_get_view` / `_get_view_cache_key` overrides on `account.analytic.line`, `project.project` and `project.task`, plus `_apply_time_label` / `_apply_timesheet_label`, unify the time field labels. `res.company._is_timesheet_hour_uom` and `_timesheet_uom_text` are removed.
- Extra validation: the project, the task and the analytic accounts of a timesheet must belong to the same company, and timesheets on a private task are rejected (on write as well).

## How your habits should change

- On the project form, fill the **analytic notebook (one account per plan)** instead of a single Analytic Account.
- If you use analytic applicability, set the required plans on the project **before** logging time.
- You can no longer log time on a **private task**.
- Editing or creating a timesheet linked to a validated sales order line is now blocked.

## What you gain by migrating

- **Multi-axis profitability**: timesheet costs feed every plan configured on the project.
- **Fewer silent errors**: explicit messages for missing plans, mixed companies, private tasks and non-updatable lines.
- **Faster** portal access to timesheets and clearer, unified field labels.
