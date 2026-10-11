# hr_recruitment_skills migration guide (17.0 -> 18.0)

## What's new for users

Odoo 18 introduces a **Candidate** record in Recruitment: a person (contact details, skills, expected salary, history) is now distinct from an **application**, which is the link between that person and one job position. This addon follows that change, so skills now belong to the candidate and are shared by all of their applications.

From the official release notes, the parts that concern this addon are:
- **New Candidate model** – one candidate can hold several applications at the same time.
- **Skills on a job position** – expected skills are matched against the candidate pool to find suited candidates.

The same release notes also mention job-board integrations (Monster), referral campaigns, duplicate-application management, email parsing rules, conditional job-form questions, mass-mail templates, offer refusal and the salary configurator. Those are not part of this addon (some are Enterprise-only) and are out of scope here.

## Technical data model changes

Models
- Removed: `hr.applicant.skill` ("Skill level for an applicant").
- Added: `hr.candidate.skill` ("Skill level for a candidate"), linked by `candidate_id` (required, cascade delete), uniqueness on `(candidate_id, skill_id)`.

`hr.applicant`
- Removed fields: `applicant_skill_ids`, `is_interviewer`, `matching_skill_ids`, `missing_skill_ids`, `matching_score`.
- Removed methods: `_compute_is_interviewer`, `_compute_skill_ids`, `_compute_matching_skill_ids`, `_get_employee_create_vals`, `_update_employee_from_applicant`, `action_add_to_job`.
- New related fields: `candidate_skill_ids` (One2many on `candidate_id.candidate_skill_ids`) and `skill_ids` (Many2many on `candidate_id.skill_ids`), both writable.

`hr.candidate` (new in this addon)
- `candidate_skill_ids` (One2many, "Skills"), `skill_ids` (stored computed Many2many), `matching_skill_ids`, `missing_skill_ids`, `matching_score`.
- Methods: `_compute_skill_ids`, `_compute_matching_skill_ids`, `_get_employee_create_vals`, `_update_employee_from_candidate`, `action_create_application` (creates one application per candidate for the job in context).

`hr.job`
- `action_search_matching_applicant` is renamed `action_search_matching_candidates`; it now searches `hr.candidate` records instead of applications, excludes candidates who already applied to the job, and opens a candidate list view.

Existing skill lines are migrated onto the candidate record.

## How your habits should change

- **Enter skills on the candidate**, not on the application. The Skills section is still visible on the application form, but the data is stored on the candidate and shared by all of that person's applications — enter a skill once instead of once per application.
- **Matching score, matching skills and missing skills** now live on the candidate and are computed against the job in context.
- **"Search matching candidates" from a job position** now returns candidates from the pool who have not applied yet, rather than applications linked to other jobs: use it as a sourcing tool.
- **Skills transfer to the employee** at hiring from the candidate record.
- The applicant-level fields `is_interviewer`, `matching_score`, `matching_skill_ids` and `missing_skill_ids` no longer exist: saved filters, favourites, automated actions or Studio rules referring to them must be updated.
- Creating an application for an existing candidate is now a separate step (`action_create_application` from the candidate).

## What you gain by migrating

- A real candidate database: one person, one skill profile, many applications — no more re-entering skills for each job.
- Cleaner ATS matching: compare a job's expected skills with the whole candidate pool and surface people who have not applied yet.
- Skills follow the person through hiring into the employee record.
- Alignment with Odoo 18's new recruitment core, keeping future Odoo updates compatible.
