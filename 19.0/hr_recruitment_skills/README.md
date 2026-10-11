# hr_recruitment_skills migration guide (18.0 -> 19.0)

## What's new for users

- **Talent pool replaces candidates.** `hr.candidate` no longer exists; its data, including skills, now lives on the applicant. Reusable profiles ("talents") are kept in the talent pool and can be used to generate applications for other job positions.
- **Job matching redesign.** Applicants now show a matching score computed from their skills **and** their degree, compared with the skills and degree expected on the job position. A simple count of shared skills is no longer used.
- **Skill levels count.** Each expected skill carries a level, and the score reflects how close the applicant's level is to the expected one.
- **Certifications.** Applicant skills support validity dates and certifications; if a certification has expired, the most recent one is used for the matching calculation.

The Recruitment section of the 19.0 release notes confirms the talent pool and the job-position matching gauge. The other release-note sections (Documents, Localizations, Appraisals, etc.) do not concern this addon.

## Technical data model changes

Models removed: `hr.candidate`, `hr.candidate.skill`.

`hr.applicant.skill`: now inherits `hr.individual.skill.mixin` (validity dates, certification logic); default order becomes `skill_type_id, skill_level_id desc`; its own `skill_id` / `skill_level_id` computes and constraints are dropped in favour of the mixin.

`hr.applicant`:
- Added `current_applicant_skill_ids` (computed, writable), used by matching so only current, valid skills count.
- `candidate_skill_ids` and `candidate_id` are removed; `applicant_skill_ids`, stored `skill_ids`, `matching_skill_ids`, `missing_skill_ids` remain.
- `matching_score` is now an **Integer** (was Float "Matching Score(%)"), and depends on `current_applicant_skill_ids`, `type_id`, `job_id`, `job_id.job_skill_ids`, `job_id.expected_degree`.
- The matching context key `active_id` is replaced by `matching_job_id`.
- `create()` / `write()` now also accept `current_applicant_skill_ids` and keep the linked talent-pool applicant skills synchronised.

`hr.job`:
- Expected skills are read from `job_skill_ids` (skill lines with a level) plus `expected_degree`, instead of a plain `skill_ids` many2many.
- New computed field `applicant_matching_score` (context `active_applicant_id`).
- `action_search_matching_candidates` renamed to `action_search_matching_applicants`; it now lists applicants from other positions whose skills match.

## How your habits should change

- Read the score as a **fit percentage**, not as a number of matching skills.
- Keep applicant skills up to date with the right level and validity dates: expired certification data is handled, missing data is not guessed.
- Fill in expected skills and the expected degree on the job position before trusting the matching score.
- Use the talent pool instead of candidates to reuse profiles; skill changes made on an application are propagated to the linked talent.
- Existing candidate-based data must be converted to applicants and talents during the upgrade.

## What you gain by migrating

- Fairer, level-aware matching that also accounts for degrees, displayed as a percentage you can compare at a glance.
- Certification and validity-date handling shared with the standard Odoo skill model, so applicant skills behave consistently with employee skills.
- A single record per person to maintain: no more duplicated candidate/applicant skill lists.
- You stay on the supported 19.0 codebase, with the standard upgrade scripts carrying your candidates over to the talent pool.
