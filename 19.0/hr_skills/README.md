# hr_skills migration guide (18.0 -> 19.0)

## What's new for users

- **Skills and certifications are now cleanly separated.** Certifications get a dedicated menu item and their own tab on the Employee form. A skill type can be flagged as a certification; certified skill types are shown with a medal marker.
- **Validity periods.** Skills and certifications carry *Validity Start* / *Validity Stop* dates. The Employee form exposes a "current skills" view that only shows what is still valid today, while expired certifications remain visible for history.
- **Faster skill entry.** Adding or updating a skill on an employee opens a dedicated dialog that is pre-filled, so you no longer juggle skill type / skill / level dropdowns on the employee form itself.
- **Resume improvements.** Resume lines gained an external URL and colour handling, so external trainings and courses can be linked from the employee resume (part of the broader resume / LMS rework).

## Technical data model changes

**Models**
- New abstract model `hr.individual.skill.mixin`, shared by `hr.employee.skill` and `hr.job.skill`. Field definitions and constraints were moved here.
- **`hr.employee.skill.log` is removed** (file deleted). There is no longer a separate, department-scoped skills history model.

**Fields added**
- `hr.employee`: `current_employee_skill_ids`, `certification_ids` (computed, writable).
- `hr.employee.public`: same two fields (related, read-only).
- `hr.skill.type`: `sequence`, `levels_count`, `is_certification`.
- `hr.employee.skill` / `hr.job.skill` (via mixin): `valid_from`, `valid_to`, `levels_count`, `certification_skill_type_count`, `is_certification` (related), `display_warning_message`.
- `hr.job`: `current_job_skill_ids`, `skill_ids`.
- `hr.resume.line`: `color`, `external_url`.

**Field behaviour changes**
- `hr.skill.level` ordering changed from `level_progress desc` to `level_progress`.
- `hr.skill.type` ordering changed from `name` to `sequence, name`.
- `hr.skill.level._compute_display_name` (the "name (x%)" dropdown label) was removed.
- Writing skill fields on `hr.employee` is now transformed into archive/create commands; the old department-driven log creation is gone.

**Method changes**
- Removed from `hr.employee.skill`: `create`, `write`, `_create_logs`, `_compute_skill_id`, `_compute_skill_level_id`, `_compute_display_name`, `_check_skill_type`, `_check_skill_level` (logic moved to the mixin).
- Added on the mixin: `_create_individual_skills`, `_write_individual_skills`, `_expire_individual_skills`, `_get_transformed_commands`, `_get_overlapping_individual_skill`, `_check_date`, `_check_not_overlapping_regular_skill`, `_onchange_valid_date`, `_can_edit_certification_validity_period`, `_linked_field_name`.
- Added on skills: `open_hr_employee_skill_modal`, `action_save`, `get_current_skills_by_employee`.
- Added on `hr.employee`: `_compute_certification_ids`, `_compute_current_employee_skill_ids`.
- **`res.users` fields removed**: hr_skills no longer extends `res.users`; skills and resume lines are no longer available on the user preferences / profile form.

## How your habits should change

- **Do not look for the old "Skills History" log.** History is now the list of skill and certification records themselves, each with a validity period.
- **Editing is not overwriting.** Changing a level archives the previous record (`valid_to` = yesterday) and creates a new one. This is intentional: it preserves traceability. Consequently records can only be truly deleted when created within the last 24 hours or already expired; otherwise they are archived.
- **Constraints are stricter.** One active skill per skill is allowed for an employee; several certifications with the same skill and level are allowed only if their date ranges differ. Overlaps and duplicates are rejected with an error listing the conflicting records, and a stop date earlier than the start date is blocked.
- **Certifications are managed from their tab/menu**, not from the generic skills list.
- **Skills are no longer self-editable from Preferences**; they are maintained on the employee record (by HR or the employee form).
- **Job position skills** do not expose the certification validity period: validity dates apply to employee records.

## What you gain by migrating

- Auditability without a side table: every level change is a dated record, so you can see what an employee knew and when.
- Certifications tracked with real validity windows — expiries are visible instead of being tacit knowledge.
- Consistent rules for employees and job positions thanks to the shared mixin, with clearer error messages on overlaps.
- Cleaner data entry through the dedicated skill dialog on the employee form.
