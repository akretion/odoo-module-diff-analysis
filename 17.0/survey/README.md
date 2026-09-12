# survey migration guide (16.0 -> 17.0)

Scope: the Community `survey` addon, migrating from 16.0 to 17.0. This summary is built from the 16.0 → 17.0 code diff and from the Odoo 17 release-notes points that actually apply to this module.

## What's new for users

- **Triggering answers**: a question can now be triggered by *several* answers, possibly coming from different previous questions. In 16.0 a question could only depend on one triggering question and one triggering answer.
- **No more "Conditional Display" checkbox**: a question is conditional as soon as it has at least one triggering answer; leave the field empty for a question that must always be displayed.
- **Multiple choice answer display**: improved display of multiple choice questions and their answers.
- **Duplicate questions**: users can duplicate a question inside a survey.
- **Free navigation**: survey takers can navigate freely in the survey, including among mandatory questions.
- **Submission notification**: get notified when a survey form is submitted.
- **Preconfigured survey types**: surveys come with a type that preconfigures the relevant settings.
- **Cleaner results**: questions without a correct answer are ignored in the results graph.

Out of scope for this module: the release-notes "Industries" section (data modules) has nothing to do with this addon. Studio-customizable certificate reports and presenter tooltips are Studio / live-session extras — do not count on them. Results drill-down and scoring after each page are not confirmed for this module.

## Technical data model changes

Removed from `survey.question`:
- `is_conditional` (Boolean), `triggering_question_id` (Many2one), `triggering_answer_id` (Many2one).

Added:
- `triggering_answer_ids` (Many2many to `survey.question.answer`, stored, editable) — the answers that trigger the question.
- `triggering_question_ids` (Many2many, computed, not stored) — convenience for display.

Other impacts:
- SQL constraints `conditional_questions_have_triggering_question` and `triggered_questions_have_triggering_answer` were dropped.
- `survey.survey.has_conditional_questions` and `is_attempts_limited` are now computed from `triggering_answer_ids`.
- Copying a survey or a question remaps triggering answers (Many2many) instead of a question/answer pair.
- The `unlink()` overrides were removed: deleting a question or an answer no longer resets dependent questions automatically.
- `survey.question.answer`: order is now `question_id, sequence, id`; `_rec_names_search` added; new computed `display_name` "Question title : Answer value", truncated at 90 characters (`MAX_ANSWER_NAME_LENGTH`).
- Conditional display is evaluated by intersecting answer sets: `_get_conditional_values()` returns `triggering_answers_by_question` (question → answers) instead of `triggering_answer_by_question`, and `_get_conditional_maps()` returns defaultdicts. No public method signature change was detected by the diff tool, but returned contents changed.

## How your habits should change

- Stop using the single "triggering answer" picker: open Triggering Answers and select as many answers as needed, even from different questions.
- You no longer tick a "Conditional Display" box. If a question must always appear, simply leave Triggering Answers empty.
- The "Is misplaced?" indicator now checks *all* triggers of a question, not just one.
- Duplicate questions to save setup time instead of recreating them.
- Expect respondents to move around freely, including back to mandatory questions.
- Rely on the submission notification instead of polling survey results manually.

## What you gain by migrating

- **Richer questionnaires**: conditional logic can combine answers from several questions, so branching is simpler and surveys stay short and dynamic.
- **Simpler configuration**: one less checkbox to keep in sync, fewer inconsistent states, and less manual cleanup when deleting questions or answers.
- **Better experience** for respondents (multiple choice display, free navigation) and for editors (question duplication, preconfigured survey types).
- **Cleaner reporting and follow-up**: questions without correct answers no longer pollute result graphs, and survey managers are notified when a form is submitted.
