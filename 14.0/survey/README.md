# survey migration guide (13.0 -> 14.0)

Community-edition summary of the `survey` addon changes between Odoo 13.0 and 14.0; release-notes items not confirmed by the code are omitted.

## What's new for users

- **Live sessions.** Run a survey with an audience: the host starts a session, shares a join link and controls the pace, one question at a time, pushed to every attendee screen.
- **Live settings.** "Reward quick answers" (speed-based scoring) and a per-question time limit.
- **Host screens.** Current question, timer, number of answers received, live results for the question and, on scored surveys, the attendee ranking.
- **Attendee experience.** Waiting screen, automatic jump to the current question, countdown, and confirmation before the next question.
- **Nicknames.** A text question flagged "Save as user nickname" identifies attendees in rankings (otherwise "Anonymous").
- **Conditional questions.** Question B appears only if a chosen answer to question A was selected; answers to hidden questions are cleared and excluded from scoring. Ignored during live sessions and with randomized selection.
- **Sharing.** Short survey link based on the first 6 characters of the token, plus a code-entry page.

## Technical data model changes

Renames (the ORM migrates data, but filters, exports, templates, server actions and custom code must be updated):

- model `survey.label` -> `survey.question.answer`
- `survey.question`: `labels_ids` -> `suggested_answer_ids`, `labels_ids_2` -> `matrix_row_ids`; `question_id_2` -> `matrix_question_id` on the answer model
- `survey.survey`: `thank_you_message` -> `description_done`, `certificate` -> `certification`, `passing_score` -> `scoring_success_min`
- `survey.user_input`: `quizz_score` -> `scoring_percentage`, `quizz_passed` -> `scoring_success`, `token` -> `access_token`, `attempt_number` -> `attempts_number`, `is_time_limit_reached` -> `survey_time_limit_reached`, `question_ids` -> `predefined_question_ids`
- `survey.user_input.line`: `value_suggested` -> `suggested_answer_id`, `value_suggested_row` -> `matrix_row_id`
- Question types `free_text` -> `text_box`, `textbox` -> `char_box`; answer types `text` -> `char_box`, `number` -> `numerical_box`, `free_text` -> `text_box`; stored values `value_text` -> `value_char_box`, `value_number` -> `value_numerical_box`, `value_free_text` -> `value_text_box`

New fields:

- `survey.survey`: `session_state`, `session_question_id`, `session_question_start_time`, `session_question_answer_count`, `session_show_ranking`, `session_speed_rating`, `has_conditional_questions`; `is_attempts_limited` is now computed/stored and forced off for public surveys without login and for surveys with conditional questions.
- `survey.question`: `save_as_nickname`, `is_time_limited`, `time_limit`, `is_conditional`, `triggering_question_id`, `triggering_answer_id`.
- `survey.user_input`: `nickname`, `is_session_answer`, `question_time_limit_reached`, `scoring_total` (raw total score, next to the percentage).

Removed: `survey.survey.category`, the `public_url` field, the redundant `question` related field; `survey.user_input.input_type` and the empty-answers autovacuum cron; `survey.question.question`.

Behaviour changes: answer scores are now computed on create/write through `_get_answer_score`, which applies the live-session speed rating; navigation is rewritten as `_get_next_page_or_question`, handling conditional questions and back navigation; new session actions (`action_start_session`, `action_end_session`) and short routes `/s`, `/s/<6 chars>`.

## How your habits should change

- To animate a live quiz, prepare the survey first (quick-answer reward, per-question time limits, nickname question), then start the session from the survey form and share the short link. The layout switches to one question per page; one session per survey; ending it marks attendee answers as done.
- Configure conditional display per question. It is ignored in live sessions and with randomized selection, and deselecting the triggering answer clears the answers of the question it displayed.
- Update every place using old technical names (saved filters, exports, automated actions, email templates, custom modules); business screens are unchanged, but old names no longer resolve.
- Options based on `category`, `input_type`, the public-URL field or empty-answer clean-up are gone; use access/invite tokens and the URL methods instead.

## What you gain by migrating

- A built-in live quiz mode (timer, speed scoring, live ranking) for webinars, events and training, with no extra module.
- Conditional questions, so respondents only see relevant questions, with correct scoring.
- A cleaner, self-explanatory data model, easier to extend, report and integrate.
