# survey migration guide (13.0 -> 14.0)

## What's new for users

**Conditional questions.** A question can now be shown only if a specific answer was selected in an earlier question (Question B appears only if "Choice 1" was picked in Question A). Answers to questions that were never displayed are cleared, so scores and printed reports stay correct. The mechanism is ignored for randomized question selection and inside live sessions.

**Live sessions.** The survey app gains a live session mode, with an improved host interface showing charts, questions and a live leaderboard of participants.

**Design.** Pictures and videos can be used as backgrounds or to illustrate questions, and new layouts are available.

**Building and sharing.** A description and sections can be added directly from the frontend form; the share link is optimised and customisable; transitions are loaded in AJAX (no full page reload) and the website header, footer and live chat are hidden while answering.

## Technical data model changes

Two kinds of changes: a large naming/cleanup refactor, and the new conditional-display fields.

Removed:
- `survey.question.question` (redundant related on `title`; `_rec_name` is now `title`).
- `survey.survey.category` and `survey.survey.public_url` (URLs are now returned by the new `get_start_url()` / `get_print_url()` methods).
- `survey.user_input.input_type` (manual vs. invitation) and the `do_clean_emptys` autovacuum job that deleted empty manual answers.
- `action_start_survey()` / `action_print_survey()` now accept an `answer` argument.

Renamed model: `survey.label` → `survey.question.answer`.

Renamed fields:
- survey.survey: `thank_you_message` → `description_done`, `certificate` → `certification`, `passing_score` → `scoring_success_min` ("Success %", no longer required).
- survey.question: `labels_ids` → `suggested_answer_ids`, `labels_ids_2` → `matrix_row_ids`; question types `free_text` / `textbox` → `text_box` / `char_box`.
- survey.question.answer: `question_id_2` → `matrix_question_id`.
- survey.user_input: `quizz_score` → `scoring_percentage`, `quizz_passed` → `scoring_success`, `token` → `access_token`, `attempt_number` → `attempts_number`.
- survey.user_input.line: `value_suggested` → `suggested_answer_id`, `value_suggested_row` → `matrix_row_id`, `value_text` → `value_char_box`, `value_number` → `value_numerical_box`, `value_free_text` → `value_text_box`; answer types aligned (`char_box`, `numerical_box`, `text_box`).

Added (conditional display): `survey.question.is_conditional`, `triggering_question_id`, `triggering_answer_id`; `survey.survey.has_conditional_questions`; `is_attempts_limited` is now stored/computed.

## How your habits should change

- Reporting, filters, exports, e-mail templates and custom code using the old names (`quizz_score`, `labels_ids`, `passing_score`, `public_url`, `value_text`…) must be updated to the new names.
- The Manual/Invitation distinction is gone; use `invite_token` to identify answers created from an invitation.
- To share a survey, use its share/start link (`get_start_url()`) instead of the removed `public_url` field.
- Question and answer types are renamed: imports or XML data your teams maintain must be aligned.
- Conditional questions are configured on the question itself; enabling them disables the limited-number-of-attempts option, and they do not apply to randomized selection or live sessions.
- Empty manual answers are no longer purged automatically: clean them up yourself if needed.

## What you gain by migrating

- Surveys that adapt to answers: shorter, more relevant forms and better completion rates.
- Live sessions with leaderboard: run quizzes and training sessions interactively.
- A more modern look and smoother, AJAX-based navigation.
- A cleaner, more consistent data model (`survey.question.answer`, `scoring_*` fields): easier to customise, report on, and to carry forward to future versions.
- Everything above ships with the Community `survey` app; no Enterprise licence is required.
