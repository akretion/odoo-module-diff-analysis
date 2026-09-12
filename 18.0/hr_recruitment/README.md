# hr_recruitment migration guide (17.0 -> 18.0)

## What's new for users

**One candidate, many applications.** Recruitment now separates the *candidate* (a person: name, email, phone, LinkedIn, degree, tags, availability) from the *application* (that person applying to a specific job position: stage, source, proposed salary, evaluation, refusal). A candidate keeps all its applications and their history.

**Duplicates are surfaced.** Candidate forms show how many similar candidates exist (same email or phone); each application shows how many *other applications* the same candidate has. Applications coming from the website also flag duplicates.

**Email parsing rules (job platforms).** Recruiters can define job-board parsing rules (e.g. a regex) so the applicant name is extracted automatically from generic application emails. This replaces the old email blacklist mechanism.

**Better communication.** Interviewers are notified when assigned to an application and unsubscribed when removed. Email/SMS can be sent to candidates directly from the list view, like for applications.

**Cleaner UI.** Job position creation was simplified, labels and placeholders cleaned up, "CV" renamed "Résumé", properties added on job positions and candidates, a confirmation dialog appears when archiving a job position that still has applications, and demo recruitment data can be loaded on demand.

**Access rights.** Recruitment access rights no longer affect employee access rights.

## Technical data model changes

New model `hr.candidate` (blacklist, phone and activity mixins), holding `applicant_ids`, `application_count`, contact fields, `linkedin_profile`, `type_id`, `availability`, `categ_ids`, `priority` (computed from applications), `employee_id`, `meeting_ids`, `attachment_ids`, `candidate_properties`.

`hr.applicant`: `candidate_id` is now required; `partner_name`, `email_from`, `partner_phone`, `linkedin_profile`, `type_id`, `availability`, `color` and `employee_id` are related to the candidate. Removed: `emp_id`, `partner_mobile`, `description`, `application_count` (replaced by `applicant_notes` and `other_applications_count`). `_rec_name` is `partner_name`, and the model no longer inherits the blacklist/phone mixins.

`hr.employee`: `applicant_id` (One2many on `emp_id`) becomes `candidate_id` (One2many on `employee_id`).

`hr.job`: new `industry_id`, `date_from`, `date_to`, `job_properties`; `no_of_hired_employee` is computed; changing `date_to` updates hired candidates' availability.

`calendar.event`: new stored `candidate_id`. `res.company`: new `candidate_properties_definition` and `job_properties_definition`. `hr.job.platform` gains `create`/`write` overrides. Methods renamed/added: `action_makeMeeting` -> `action_create_meeting`, `action_open_other_applications`, `action_open_similar_candidates`, `create_employee_from_candidate`.

## How your habits should change

- The candidate is the master record: fixing an email or phone on an application updates the candidate and all its applications.
- Tags belong to the candidate and are shared across its applications.
- The employee link now lives on the candidate; a candidate linked to an employee cannot be deleted — archive it instead.
- Duplicate detection uses email and phone only (the mobile field was removed).
- Applications received by email now create a candidate first.
- Some actions moved or were renamed: create a meeting from the candidate/application, and open "Other applications" instead of similar applicants.

## What you gain by migrating

A true applicant-tracking workflow: repeat candidates are recognised instead of duplicated, applications stay clean and comparable across job positions, interviewers are properly notified, candidates can be contacted in bulk, and job positions carry their own properties and mission dates. Day-to-day usage stays familiar, but this is a data-structure change — plan a data check on candidates, applications, employee links and tags.

*Note: some items in the official 18.0 Recruitment release notes (job-board posting such as Monster, referral campaigns, salary configurator, skill matching) are Enterprise or belong to other addons and are not covered by this module.*
