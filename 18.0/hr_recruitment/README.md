# hr_recruitment migration guide (17.0 -> 18.0)

## What's new for users

**Candidates and applications are now separate.** Odoo 18 introduces a *Candidate* (the person: name, contact details, résumé, skills, expected salary, availability) and keeps *Applications* as the link between a candidate and a job position. The same candidate can apply to several positions and you can jump to their other applications from a smartbutton. Creating an application without an existing candidate creates it automatically.

**Duplicate detection.** Similar candidates are matched on email and phone (mobile-specific fields were dropped).

**Contact candidates directly.** You can send an email, with a template, to one or several candidates from the candidate list view.

**Job positions.** Job positions now carry their own Properties. The department manager and the recruiter are automatically subscribed as followers. Archiving a job position that has applications asks for confirmation. Interviewers receive an email when they are assigned.

**Naming and UX.** "Applied Job" becomes "Job Position"; salary fields are labelled "Proposed" and "Expected"; "CV" becomes "Résumé"; the applicant "Description" tab becomes "Notes"; placeholders and empty-list texts were rewritten; demo recruitment data can be loaded from the settings; Graph and Pivot reports now show the same information.

**Access rights.** Recruitment access rights no longer affect Employee access rights.

Not part of this Community addon: referral campaigns, job-board (Monster) posting, the salary configurator.

## Technical data model changes

New model `hr.candidate`: partner name, email, phone, LinkedIn, degree, availability, tags, employee link, applications, meetings, attachments, similar-candidate count. `hr.applicant` now carries the job-specific data (job, stage, proposed salary, source/medium/campaign, interviewers, refuse
