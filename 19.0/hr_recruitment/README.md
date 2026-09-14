# hr_recruitment migration guide (18.0 -> 19.0)

## What's new for users

- **Talent pool replaces candidates.** The candidate system has been replaced by a talent pool system: you can turn an existing application into a reusable "talent", group talents into talent pools, and use them to create applications for other job positions. Simple hiring flows stay simple, while head-hunting style re-engagement becomes possible again.
- **Job matching.** The skills redesign adds a job position matching indicator showing how suitable an applicant is for a role, based on their skills and their degrees, compared with what the job position expects.
- **Campaign tracking.** Applicants can still be tracked by campaign.

The release notes say nothing else specific to this addon (the remaining recruitment/Documents entries concern other apps or Enterprise-only features and are not part of this Community addon).

## Technical data model changes

**Removed**
- The whole `hr.candidate` model.
- On `hr.applicant`: `candidate_id` and the related fields it carried (`email_from`, `email_normalized`, `partner_phone`, `partner_phone_sanitized`, `linkedin_profile`, `type_id`, `availability`, `color`, `employee_id`, `emp_is_active`, `employee_name` are now plain fields on the applicant); `other_applications_count` and its compute are replaced by `application_count`; the tag compute `_compute_categ_ids` is gone.
- On `calendar.event`: `candidate_id` (and its compute, plus `default_candidate_id` context handling).
- On `hr.job`: mission dates `date_from` / `date_to`, `user_id` (Recruiter), `currency_id` / `compensation`, `activities_overdue` / `activities_today`; the `action_open_late_activities`, `action_open_today_activities` and `action_job_board_modules` actions; the automatic creation of a LinkedIn source when a job position is created.

**Added**
- `hr.talent.pool` model.
- On `hr.applicant`: `talent_pool_ids`, `pool_applicant_id`, `is_pool_applicant`, `is_applicant_in_pool`, `talent_pool_count`, plus `application_count`, and the `mail.thread.blacklist` / `mail.thread.phone` mixins.
- On `hr.job`: `job_source_ids`, `expected_degree`, `activity_count` (single activity counter).
- On `hr.recruitment.degree`: `score` (0 to 1, constrained).
- On `hr.recruitment.source`: `create_and_get_alias()`.

**Behaviour changes**
- Applicant identity data now lives on the applicant. Email and phone are computed from the contact and write back to it, creating the contact automatically (a contact name is required when one is created).
- Duplicate detection is based on shared email, sanitized phone or LinkedIn profile, excludes talents, and is scoped per company.
- Tags are no longer inherited from a candidate, and CV/attachments are no longer copied from candidate to applicant at creation.
- Availability is no longer auto-updated from job position dates.

## How your habits should change

- **Candidates are gone.** Keep creating applications as usual; to keep someone on file, promote the application to a talent and place it in a talent pool.
- **Refusing** an applicant no longer uses a cog menu: a visible action button replaces it, the wizard uses the standard email composer, and duplicate applications are shown as tags (also when refusing several applicants at once).
- **Job positions**: trackers are now a page rather than a stat button; key actions are reachable directly from the job position view.
- **Skills**: define expected skills and the expected degree on the job position to benefit from the matching indicator.

## What you gain by migrating

- A lighter recruitment flow: no candidate record to maintain, fewer clicks for occasional hiring.
- Talent pools restore re-engagement and multi-position sourcing for teams that need it.
- Skills-based matching with degree scoring helps shortlist candidates faster.
- Cleaner screens and a more approachable refusal/duplicate experience.
- A supported, upgrade-safe data model, since candidate data is migrated onto applications and talents.
