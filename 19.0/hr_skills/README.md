# hr_skills migration guide (18.0 -> 19.0)

## What's new for users

- **Certifications are now a first-class concept.** A skill type can be flagged as a certification. Certifications get their own menu entry and a dedicated tab on the Employee form, separate from regular skills.
- **Skill management has been reworked.** Employee skill lines now carry a *Validity Start* / *Validity Stop* period, so a skill can have a history (e.g. "English A2 from January to June", then "English B1").
- **No more "Skills History" screen.** History is no longer stored in a separate log; it is reconstructed from the validity dates on the skill lines themselves.
- **Improved creation dialog.** Adding a skill or a certification from an employee is done through a dedicated, simplified pop-up with a quick "Save" action.
- **Clearer validation.** Odoo blocks overlapping periods, an end date before the start date, or a skill that does not belong to the selected skill type.

Only these items relate to `hr_skills`; the rest of the 19.0 release notes concerns other apps.

## Technical data model changes

**New model**
- `hr.individual.skill.mixin` (abstract): shared logic for skill/certification lines. Fields: `skill_id`, `skill_level_id`, `skill_type_id`, `level_progress`, `color`, `valid_from` (default today), `valid_to`, `levels_count`, `certification_skill_type_count`, `is_certification` (related to `hr.skill.type.is_certification`), `display_warning_message`. Constraints: `_check_not_overlapping_regular_skill`, `_check_date`, `_check_skill_type`, `_check_skill_level`.

**Removed model**
- `hr.employee.skill.log` ("Skills History") is deleted, along with `_create_logs()` and every automatic log creation in `hr.employee.skill` and `hr.employee.write()`.

**Changed**
- `hr.employee.skill` now `_inherit = 'hr.individual.skill.mixin'`; gains `valid_from`, `valid_to`, `is_certification`. The SQL constraint `unique (employee_id, skill_id)` is dropped and replaced by overlap checks (one active line per skill, several certifications allowed if their periods differ).
- `level_progress` is no longer stored or aggregated (previously stored with the `avg` aggregator).
- `hr.employee`: new `current_employee_skill_ids` and `certification_ids` (computed, writable; `create()`/`write()` fold them into transformed `employee_skill_ids` commands). `write()` no longer creates log entries when `department_id` changes.
- `hr.employee.public`: added related `current_employee_skill_ids` and `certification_ids` (read-only).
- `hr.employee.skill`: new `_linked_field_name()`, `get_current_skills_by_employee()`, `open_hr_employee_skill_modal()`, `action_save()`, plus inherited `_create_individual_skills()` / `_expire_individual_skills()`.
- `res.users`: the whole `hr_skills` extension is removed (`resume_line_ids`, `employee_skill_ids`, `current_employee_skill_ids`, `certification_ids`, `SELF_READABLE_FIELDS` / `SELF_WRITEABLE_FIELDS`); view `hr.res_users_view_form_simple_modif` removed.

## How your habits should change

- Stop looking for the Skills History log: read history from the validity dates on the skill lines.
- Editing a skill does not overwrite it. Odoo archives the previous line and creates a new one.
- Removing a current skill effectively archives it (validity stop set to yesterday); only very recent lines are truly deleted. Certifications, however, can be deleted at any time.
- Skills self-service from the Preferences/Profile form is gone: manage skills from the Employee form.
- Flag the relevant skill types as certifications to get the dedicated menu and tab.
- Existing 18.0 data is handled by the upgrade scripts shipped with the migration.

## What you gain by migrating

- Proper certification tracking: expiry dates, a dedicated tab and menu, and explicit warnings instead of silent overwrites.
- A reliable, audit-friendly skill history without an extra log model, so past skill levels stay consistent in reports.
- Cleaner employee records (archiving instead of deleting) and fewer data-entry errors thanks to overlap and date validation.
- A simpler, faster skill-creation dialog for HR users.
- Skills are managed in one place — the employee — while user preferences stay focused on user settings.
