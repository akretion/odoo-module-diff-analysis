# hr_recruitment_skills migration guide (17.0 -> 18.0)

## What's new for users

Odoo 18 splits the old applicant record into a **Candidate** (the person: name, contact details, skills, expected salary, full history) and an **Application** (the link between a candidate and one job position: source, stage, proposed salary, evaluation). A candidate can now apply to several job positions while keeping a single, shared skill profile.

Skills follow the candidate. What you enter on one application is the candidate's skill set, reused on every other application. Job positions get a new **Search Matching Candidates** action that looks up the candidate pool for people whose skills match the expected skills of the position and who have not applied to it yet.

*(Release notes covered: "New Candidate model" and "Skills on job position".)*

## Technical data model changes

**Models**
- `hr.candidate.skill` replaces `hr.applicant.skill`: same `skill_id` / `skill_level_id` / `skill_type_id` fields, same validation rules and same "one level per skill" SQL constraint, now keyed on `candidate_id` instead of `applicant_id`.
- `hr.candidate.skill` gets the methods `_check_skill_type`, `_check_skill_level`, `_compute_skill_id`, `_compute_skill_level_id` (identical logic, renamed).

**hr.candidate** (newly extended by this addon)
- Added fields: `candidate_skill_ids` (One2many), `skill_ids` (Many2many, computed and stored), `matching_skill_ids`, `missing_skill_ids`, `matching_score`.
- Added methods: `_compute_skill_ids`, `_compute_matching_skill_ids`, `_get_employee_create_vals`, `_update_employee_from_candidate`, `action_create_application`.

**hr.applicant**
- Keeps `candidate_skill_ids` and `skill_ids`, but as *related, editable* fields pointing to the candidate.
- Removed: `applicant_skill_ids`, `matching_skill_ids`, `missing_skill_ids`, `matching_score`, `is_interviewer`, and the methods `_compute_is_interviewer`, `_compute_skill_ids`, `_compute_matching_skill_ids`, `_get_employee_create_vals`, `_update_employee_from_applicant`, `action_add_to_job`.

**hr.job**
- `action_search_matching_applicant` is renamed `action_search_matching_candidates`. The action now searches `hr.candidate` records, excludes candidates already linked to the job, and opens the candidate list/form view.

## How your habits should change

- Maintain skills on the **candidate** form; it is the single source of truth. Editing skills from an application still works and writes back to the candidate.
- Matching score, matching skills and missing skills are now displayed on the **candidate**, not on the application.
- The button on a job position is now **"Search Matching Candidates"** and lists candidates from the pool, not applications.
- Moving a candidate onto a job is done by creating an application from the candidate, rather than moving an application between jobs.
- Creating an employee from a candidate copies the candidate's skills; skills the employee already has are not duplicated.
- `is_interviewer` is no longer provided by this addon.

## What you gain by migrating

- **No repeated data entry**: one candidate profile can serve many applications, with skills entered once.
- **A real talent pool**: the expected skills of a job position can be matched against all candidates, not only against existing applications.
- **Cleaner history and fewer duplicates**, aligned with the Odoo 18 core Recruitment data model (candidate vs. application).
- **Skills flow through to HR**: candidate skills are carried over automatically when an employee record is created or updated.
- **Same functional behaviour** as 17.0 for skill levels, skill types and matching score, simply relocated to the candidate.
