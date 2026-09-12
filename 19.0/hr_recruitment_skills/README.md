# hr_recruitment_skills migration guide (18.0 -> 19.0)

Scope: Community edition addon `hr_recruitment_skills` (skills on applicants and job positions). 19.0 is a deep redesign: skills move from the candidate to the applicant, and the candidate model disappears.

## What's new for users

- **Talent pool instead of candidates.** The release notes state it plainly: "the candidate system has been replaced by a talent pool system". An application can be saved as a reusable talent (application template) and later used to generate applications for other job positions.
- **Job matching.** The skills redesign introduces a matching score between an applicant and a job position: **Matching Score (%)**, **Matching Skills** and **Missing Skills** on the applicant, plus a matching score on a job position opened from an applicant. The score weighs skill levels and the applicant's degree, instead of the simple skill overlap of 18.0.
- **Expected skills on a job position now carry a level**, and an expected degree is taken into account in the comparison.
- **Skills are entered directly on the applicant form**, with a reworked skill dialog and a "current skills" notion: a skill whose valid-to date is past no longer counts, and for certifications the most recent one is kept.
- Simple application-only recruitment stays possible for occasional hiring, so small teams are not forced into a complex workflow.

The release notes also mention the matching gauge chart and the Employees "Skills and certifications" cleanup. Those visuals belong to the wider HR/Enterprise material: what this Community addon ships is the computed score and skill fields described below.

## Technical data model changes

- **Removed models:** `hr.candidate` and `hr.candidate.skill`.
- **Added model:** `hr.applicant.skill` (replaces `hr.candidate.skill`), ordered by `skill_type_id, skill_level_id desc`, and now inheriting the shared `hr.individual.skill.mixin` (validity dates, certification handling).
- **`hr.applicant`:** new `applicant_skill_ids` (One2many, copied), `skill_ids`, `matching_skill_ids`, `missing_skill_ids`, and `current_applicant_skill_ids` (computed, readonly=False). `matching_score` changed from `Float` to `Integer`; matching is now computed from `current_applicant_skill_ids`, `type_id`, `job_id`, `job_id.job_skill_ids` and `job_id.expected_degree`, using context key `matching_job_id` (previously `active_id`). The old `candidate_id` and `candidate_skill_ids` fields are gone.
- **`hr.job`:** the `skill_ids` "Expected Skills" field was removed in favour of skill lines with levels (`job_skill_ids`) plus `expected_degree`; new computed `applicant_matching_score`.
- **Method signatures:** 0 modified, 12 added, 10 removed. Added: `create`, `write`, `action_add_to_job`, `_get_employee_create_vals`, `_compute_current_applicant_skill_ids`, `_compute_matching_skill_ids`, `_compute_skill_ids`, `_map_applicant_skill_ids_to_talent_skill_ids`, `_get_current_skills_by_applicant`, `_linked_field_name`, `action_search_matching_applicants`, `_compute_applicant_matching_score`. Removed: `action_search_matching_candidates`, `action_create_application`, `_update_employee_from_candidate`, and the `hr.candidate.skill` validators/computes.

## How your habits should change

- There is no more **Candidates** menu. Work with applications; use the **Talent Pool** for profiles you want to keep and reuse.
- Record skills on the **applicant form**, not on a candidate. Skills of an applicant linked to a talent are kept in sync with that talent.
- From a job position, use **Search Matching Applicants** (renamed from Search Matching Candidates). It now lists applicants matching the job's expected skills who are not already on that job.
- When you hire from an application, the applicant's skills are carried over to the new employee's skill list.
- Keep skill levels and the expected degree up to date on job positions: they directly drive the matching score, as do skill validity dates.
- Plan for the data upgrade: Odoo ships upgrade scripts (referenced by the related upgrade PRs) for candidate → applicant/talent pool conversion, but review your existing candidate skills and applicants after migration.

## What you gain by migrating

- One single, simpler object to manage (the applicant) with a reusable talent pool — less data entry, fewer duplicate records.
- A realistic matching score based on skill levels and degrees, so shortlisting applicants for a job position is faster and more meaningful.
- Clear visibility of what is missing for a candidate profile (Missing Skills), useful when discussing training or hiring decisions.
- Skills entered on an application are propagated to the employee record, keeping HR data consistent from recruitment to onboarding.
