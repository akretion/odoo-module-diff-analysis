# hr_recruitment migration guide (18.0 -> 19.0)

## What's new for users

**Talent pool replaces candidates.** The Candidate record is gone: applications now hold the person's details, and the new **Talent Pool** replaces it. Any application can be turned into a "talent", grouped into pools, and reused to create applications for other job positions — simpler for SMEs, while headhunting-style reuse remains possible.

**Skills-based job matching.** Job positions define expected skills and an expected degree; degrees now have a score (0–100%) and the applicant shows how well they match a position, with matching positions browsable.

**Job position dashboard.** The job kanban is a clearer vertical list with "New applications" / "In progress" counters, one activity counter, an employees smart button and the company name on cards.

**Campaign/source tracking.** Applications stay tracked by source, now linked from the job position.

## Technical data model changes

* **Model `hr.candidate` removed** (also `res.company.candidate_properties_definition`, `calendar.event.candidate_id`); `hr.employee.candidate_id` becomes `applicant_ids`.
* **`hr.applicant`** now owns the person data: `partner_id`, `partner_name`, `email_from/normalized`, `partner_phone(_sanitized)`, `linkedin_profile`, `type_id`, `availability`, `color`, `employee_id/name`, `categ_ids` (plain many2many), and inherits `mail.thread.blacklist` + `mail.thread.phone`.
* **New applicant fields:** `talent_pool_ids`, `pool_applicant_id`, `is_pool_applicant`, `is_applicant_in_pool` (searchable), `talent_pool_count`, `application_count` (replaces `other_applications_count`), plus "rotting" helpers for stale applications.
* **New model `hr.talent.pool`:** name, company, pool manager, description, color, tags, `talent_ids` (applicants), `no_of_talents`.
* **`hr.job`:** added `job_source_ids`, `expected_degree`, `employee_count`, `open_application_count`, `activity_count` (replaces `activities_overdue`/`activities_today`), `action_open_employees`. Removed: `user_id` (Recruiter), `currency_id`/`compensation`, `date_from`/`date_to`.
* **`hr.recruitment.degree`:** new `score` (0–1, constrained). **`hr.recruitment.source`:** new `create_and_get_alias()`.
* **Method signatures:** `message_new(msg_dict, ...)`, `_notify_get_reply_to(default=None, author_id=False)`, `toggle_active()` → `action_archive()`/`action_unarchive()`, applicant `copy_data()`.
* **New SQL indexes** on applicant email/phone and attachment content (performance).

## How your habits should change

* Stop creating Candidates: contact info, tags, CV, meetings and employee creation all happen on the application.
* Duplicates ("other applications") are found automatically by matching email, phone or LinkedIn.
* The Recruiter field on the job position is gone; recruiters/interviewers are set on applications or as job interviewers.
* Mission dates (`date_from`/`date_to`) are no longer on job positions (the job-board integration app adds them back) and availability is no longer auto-updated from the mission end date.
* Refusing uses the new action button (cog menu entry removed); the refuse wizard uses the standard email composer and lists duplicates as tags.
* Attachments/CVs live on the application; job "Trackers" is now a page in the job form.

## What you gain by migrating

* A simpler, less redundant data model: no Candidate/Application duplication, fewer records to maintain.
* Talent pools: build a reusable pool of profiles and generate applications for new openings in a few clicks.
* Faster shortlisting thanks to expected skills/degrees and applicant-position matching.
* Cleaner job dashboards (in-progress applications, activities, employees) and a more discoverable refuse flow.
* Stronger communication on applications (phone/email blacklist, bounce handling) and performance work on applicant search and attachments.
