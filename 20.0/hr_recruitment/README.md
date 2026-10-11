# hr_recruitment migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes for Recruitment describe features that belong to the Enterprise matching/talent tooling: matching-score filtering, viewing matching talents from a job position, and visibility conditions on the online job form. None of these ship in this Community addon, so they are not announced here. The notes only state a general direction (smoother integration between applicants, talent pool and job positions) without any Community-specific detail.

For Community users, the real news of this version are the changes described below: a recruiter list you can trust, tags on job positions, and salaries expressed with a currency and a pay period.

## Technical data model changes

**Recruiter is now an employee** (`hr.applicant`, `hr.job`, `hr_recruitment_reports`)
- `hr.applicant.user_id` (res.users) becomes `recruiter_id` (hr.employee, company-checked); `user_email` becomes `recruiter_email`, related to the employee's Work Email.
- `hr.job.user_id` becomes `hr.job.recruiter_id`, with the same restricted domain.
- `hr.job.favorite_user_ids` becomes `favorite_recruiter_ids` (new relation table `job_favorite_recruiter_rel`), defaulting to the current user's employee.
- The recruiter list is limited to employees whose user is internal and holds Recruitment Officer or Administrator; reports and SQL queries were updated accordingly.
- Method changes: `_compute_user` → `_compute_recruiter`; `_get_default_favorite_user_ids` → `_get_default_favorite_recruiter_ids`; new `_recruiter_domain` helper on both applicant and job; favourite computations (`_compute_is_favorite`, `_inverse_is_favorite`, `_compute_sql_is_favorite`) now work on employees. `date_open` ("Assigned") is still stamped when a recruiter is set or changed.

**Job tags and salaries**
- New model `hr.job.tag` (name, automatic colour), `hr.job.tag_ids`, tags shown in the job list and kanban, and a new "Job Tags" menu under Job Positions.
- `hr.applicant.salary_proposed` and `salary_expected`: Float → Monetary, using the new `currency_id` (company currency); new required `schedule_pay` (Hour…Year, default Month), filled from the job when the applicant is created.
- On `hr.job`: new `currency_id`, `salary_min`, `salary_max` (Monetary) and required `payment_interval` (default Month); an onchange swaps min/max when they are inverted.

**Stages and kanban**
- `hr.recruitment.stage` loses `legend_blocked/waiting/done/normal`; `hr.applicant` loses the four related `legend_*` fields.
- Kanban state "Ready for Next Stage" is renamed "Ready".
- Stage form aligned with other apps (sequence hidden in the form), colour added on stages.

## How your habits should change

- Recruiter fields now accept employees, not users: an internal user with no employee record can no longer be chosen as recruiter. Check your team's employee records first.
- Favourites on job positions are stored per employee; a user without an employee record has no favourites.
- If a 19.0 recruiter had no linked employee record, there is nothing to map the value to during the upgrade: review recruiters on open job positions and applicants after migrating.
- Custom views, filters, exports, automated actions, email templates or Studio fields referencing `user_id` / `favorite_user_ids` on applicants and jobs must be renamed to `recruiter_id` / `favorite_recruiter_ids`.
- Salary figures are now entered with the company currency and a pay period; check that the interval (Month, Year…) is correct, otherwise averages and comparisons mislead.
- Use tags to classify job positions and the Job Tags menu to maintain them.
- Stage-level custom labels ("Blocked", "Waiting"…) are gone; kanban columns use standard labels, and colours are set on the stage record.

## What you gain by migrating

- A recruiter list restricted to people who genuinely have recruitment rights: no more applicants assigned to users who cannot act on them.
- One consistent "who follows this job" concept for job positions and applicants, built on HR employees, keeping recruitment aligned with the Employees app.
- Proper monetary salary fields with currency and pay period, plus salary ranges on job positions, instead of bare numbers.
- Tags to organise and filter your job openings, and stage configuration consistent with CRM and Project.
- Cleaner, standard kanban and stage setup, which is faster to train new recruiters on.
