# survey migration guide (12.0 -> 13.0)

## What's new for users

* **Certifications and scoring.** Each suggested answer can carry a score and a
  "correct answer" flag; you set a passing score in %. When a participant
  passes, an optional certificate email template sends the document.
* **Time limit and attempts.** Limit a test's duration (with an on-screen
  timer) and the number of attempts per participant.
* **Better results.** The results page shows success rate and correct answers;
  kanban and reporting views expose participants, certified people, success
  ratio and average score.
* **Simpler status.** Configurable stages are replaced by three fixed states —
  Draft, In Progress, Closed — switched with buttons.
* **Easier authoring.** Pages and questions now live in one list: add a page or
  a question and drag them to reorder.

The 13.0 release notes also cover eLearning certifications (payments, course
sales). Those belong to other, Enterprise-oriented modules and are not part of
this Community addon.

## Technical data model changes

* `survey.page` **removed**: pages are `survey.question` records with
  `is_page = True`. `page_id`, `page_ids` and `question_ids` are computed from
  the ordered `question_and_page_ids` field on `survey.survey`.
* `survey.stage` **removed**, replaced by a static `state`
  (`draft`/`open`/`closed`) with `action_draft`, `action_open`,
  `action_close`. `stage_id` and `is_closed` are gone.
* `survey.survey` **added**: `question_and_page_ids`, `page_ids`,
  `question_ids`, `scoring_type`, `passing_score`, `is_attempts_limited`,
  `attempts_limit`, `is_time_limited`, `time_limit`, `certificate`,
  `certification_mail_template_id`, `answer_score_avg`, `certified_ratio`.
  **Removed**: `designed`, `public_url_html`, `print_url`, `result_url`,
  `email_template_id`, `quizz_mode`, `invite_count`.
* `survey.label`: `quizz_mark` → `answer_score`; new `is_correct`.
* `survey.user_input`: `date_create` removed (use `create_date`); `_rec_name`
  is now `survey_id`; added `start_datetime`, `is_time_limit_reached`,
  `quizz_passed`; `quizz_score` is a stored percentage.
* `survey.user_input_line`: `date_create` removed; `_rec_name` is
  `user_input_id`; `quizz_mark` → `answer_score`.
* Compatibility `oldname=` aliases were dropped and the Python model was split
  into one file per model. Method changes: `action_view_answers` →
  `action_print_answers`, `action_survey_results` removed,
  `action_survey_user_input_invite` → `action_survey_user_input`,
  `next_page` signature simplified.

## How your habits should change

* Stop managing survey stages; use the Draft / In Progress / Closed buttons. A
  closed survey still refuses new answers.
* Build pages inside the questions list instead of a separate Pages tab.
* Configure scoring per survey (no scoring, with or without answers at the
  end); "correct answer" and score options only show up on questions when
  scoring is enabled. Certificates require scoring.
* If you import data or use XML-RPC, adapt to the renamed and removed fields
  listed above — old field names are no longer accepted.

## What you gain by migrating

* Real assessments in Community: scoring, pass threshold, emailed certificate,
  time limit and attempt control.
* Immediate KPIs on the survey kanban (registered, attempts, certified, ratio,
  average score).
* Faster authoring with pages and questions in a single sortable list.
* A simpler status model and a leaner data model (dummy URL/date fields gone).
