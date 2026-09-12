# hr_recruitment migration guide (19.0 -> 20.0)

## What's new for users

No official 20.0 release-note extract covers `hr_recruitment`, so this guide is based on the actual 19.0 → 20.0 changes shipped in the addon (four commits).

- **Job tags.** Job Positions now carry reusable tags (new "Job Tags" window action under the Job Positions menu), shown in list and kanban views, so you can group, filter and report positions by tag.
- **Richer salary information.** Salary amounts are no longer plain numbers: they are monetary amounts in the company currency. Job Positions gain a minimum/maximum salary range plus a "Scheduled Pay" interval (Hour, Day, Week, Bi-Week, Month, Year). Entering a minimum above the maximum swaps the two values automatically.
- **Cleaner recruiter selection.** The Recruiter field only lists employees whose user account has recruitment rights (Recruitment Officer or Manager) — no more assigning applicants to users who cannot work on them.
- **Favourites are per employee.** Favouriting a job position now follows your employee record instead of any user.
- **Simpler stage configuration.** Per-stage kanban texts (Blocked / Waiting / Ready / In Progress) are no longer configurable; labels are unified with CRM and Project ("Ready" replaces "Ready for Next Stage"). Stage forms were reworked and expose a colour.

## Technical data model changes

New model: `hr.job.tag` (`name`, `color`).

- `hr.job`: added `tag_ids` (m2m to `hr.job.tag`), `currency_id`, `salary_min`, `salary_max`, `payment_interval`; `user_id` (res.users) replaced by `recruiter_id` (hr.employee, company-checked, restricted domain); `favorite_user_ids` replaced by `favorite_recruiter_ids` (hr.employee, `job_favorite_recruiter_rel`).
- `hr.applicant`: `salary_proposed` and `salary_expected` changed from `Float` to `Monetary` (related `currency_id`); added `schedule_pay` (required, default monthly, copied from the job's pay interval); `user_id` → `recruiter_id`; `user_email` → `recruiter_email` (related to the employee's work email); the `legend_*` related fields are removed; `kanban_state` "done" label becomes "Ready".
- `hr.recruitment.stage`: the `legend_blocked/waiting/done/normal` fields are gone.
- Method signatures: 1 modified, 19 added, 7 removed. Renames include `_compute_user` → `_compute_recruiter`, `_get_default_favorite_user_ids` → `_get_default_favorite_recruiter_ids`, `_compute_kpis_actions` → `_get_kpi_custom_settings`, `_order_field_to_sql` → `_compute_sql_is_favorite`. Removed: `action_send_email`, `_track_subtype`, `_track_template`. Added: `_recruiter_domain`, `_creation_message`, `_get_sync_fields`, `_track_log_get_default_subtype`, `_track_template_parameters`, `copy`, `action_job_add_applicants`, `action_open_applicant`, `_compute_applicant_name`, `_get_light_group_xmlids`. Custom code relying on those names must be updated.

## How your habits should change

- Each recruiter you assign must exist as an **employee** linked to a user holding recruitment rights; users without an employee record can no longer be selected.
- Data must be migrated: existing recruiters and favourites stored as users need conversion to employees — use the standard upgrade scripts rather than a manual import.
- Salaries are entered as currency amounts; verify the company currency and the pay interval before reporting.
- You can no longer customise kanban state wording per stage; those texts are fixed.
- Filter and group by "Recruiter" (an employee) instead of user in filters, groupings and saved reports.

## What you gain by migrating

- Recruitment data that matches reality: salaries with currency, a min/max range and a pay period.
- Fewer assignment mistakes: only qualified recruiters appear, and each applicant is tied to a real employee.
- Faster navigation through job tags, plus a consistent stage/kanban experience shared with CRM and Project.
- A maintained codebase: 19.0 customisations built on `user_id`, `favorite_user_ids` or `legend_*` will keep breaking until migrated, while 20.0 gives you the supported APIs and upgrade path.
