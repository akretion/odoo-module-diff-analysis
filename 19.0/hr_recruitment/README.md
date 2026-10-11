# hr_recruitment migration guide (18.0 -> 19.0)

## What's new for users

**Talent pools replace candidates.** The *Candidate* model is gone. The personal data that used to live on a candidate (name, contact, email, phone, LinkedIn, degree, availability, tags, employee link) now lives on the application itself, so an application is created in one step. To keep a profile for later openings, use the new **Talent Pool**: turn an application into a talent, or add applications to a pool, then reuse them to create applications for other job positions. Talents and duplicates are matched automatically on email, phone or LinkedIn.

**Skills-based job matching.** Job positions can define expected skills and an expected degree, and degrees now carry a score. Applicants get a matching gauge showing how suitable they are for the role, and you can browse the positions they match best.

**Campaign tracking.** Applicants can be tracked by campaign.

**Refreshed screens.** The job position kanban becomes an easier-to-scan vertical list with "New applications" / "In progress" indicators and one activity counter; Trackers get their own page on the job position form; the Refuse action leaves the cog menu for a visible button and uses the standard email composer; duplicate applicants are shown as tags in the refuse wizard.

## Technical data model changes

Models
- Removed: `hr.candidate`, and `calendar.event.candidate_id`.
- Added: `hr.talent.pool`.

`hr.applicant`
- `candidate_id` removed; its data is now on the applicant as own stored fields: `partner_id`, `partner_name`, `email_from`, `email_normalized`, `partner_phone`, `partner_phone_sanitized`, `linkedin_profile`, `type_id`, `availability`, `color`, `employee_id`, `emp_is_active`, `employee_name`.
- `categ_ids` is no longer computed from the candidate.
- `other_applications_count` → `application_count` (same email/phone/LinkedIn, talents excluded).
- Removed: `similar_candidates_count`, `applications_count`, `refused_applications_count`, `accepted_applications_count`, `meeting_ids`, `meeting_display_text/date`, `attachment_count`, `candidate_properties`.
- Added: `talent_pool_ids`, `pool_applicant_id`, `is_pool_applicant`, `is_applicant_in_pool`, `talent_pool_count`.
- New mixins `mail.thread.blacklist` and `mail.thread.phone`.
- `write()` propagates email/phone/LinkedIn/degree changes to the linked pool applicant; availability is no longer set from the job end date.
- `_get_similar_candidates_domain()` becomes the multi-record `_get_similar_applicants_domain(ignore_talent, only_talent)`.

`hr.job`
- Removed: `date_from`, `date_to`, `user_id` (Recruiter), `currency_id`, `compensation`, `activities_overdue`, `activities_today`, `action_open_late
