# survey migration guide (12.0 -> 13.0)

This guide summarizes what changes for the `survey` addon between Odoo 12.0 and 13.0: first what your users will notice, then the data model changes to plan for during the migration.

## What's new for users

The official 13.0 release notes for Survey highlight:

- **Certifications.** You can create certification surveys, with scoring, and set a time limit on a survey.
- **Usability and statistics.** Success rates and other statistics can be consulted directly from the kanban view.
- **Access rights.** Survey access rights have been clarified.

The release notes also mention eLearning items (courses, badges, ranks, favourites, forums, selling courses through eCommerce). Those belong to other applications, not to this addon, and are not delivered by this migration.

## Technical data model changes

**Kanban stages replaced by a static state.**
- The `survey.stage` model is **removed**, along with the fields `stage_id`, `is_closed` and the stage-related kanban behaviour.
- `survey.survey` gains a `state` selection with three fixed values: `draft` (Draft), `open` (In Progress), `closed` (Closed), default `draft`, required.
- New button methods: `action_draft()`, `action_open()`, `action_close()`.
- Code that tested `stage_id.closed` or `is_closed` must now test `state == 'closed'`.
- Existing stage records are lost: after migration each survey must be positioned in one of the three states.

**Pages merged into questions.**
- The `survey.page` model is **removed**. Pages are now `survey.question` records flagged with the new `is_page` boolean.
- New on `survey.survey`: `question_and_page_ids` (stored one2many), plus computed `page_ids` and `question_ids`.
- On `survey.question`: `title` (the question/page text), `question` (related to `title`), computed stored `page_id`, computed `question_ids`.
- `survey.user_input.last_displayed_page_id` now points to `survey.question`.

**Unused fields and methods removed.**
- On `survey.survey`: `is_designed`, `email_template_id`, `public_url_html`, `print_url`, `result_url`.
- On `survey.user_input`: `date_create` (use standard `create_date`), `result_url`, `print_url`, and the `action_survey_results()` button.
- On `survey.user_input_line`: `date_create`.
- URL-related fields are replaced by links built directly in code and templates.

**Renamed methods and fields.**
- `action_view_answers()` → `action_print_answers()`; `action_survey_user_input_invite()` → `action_survey_user_input()`.
- Statistics: `invite_count` removed; `answer_count` is now labelled "Registered", `answer_done_count` "Attempts"; new `answer_score_avg` and `certified_ratio`; `certified_count` is computed in `_compute_survey_statistic`.
- `quizz_score` on answers is now stored (`compute_sudo`) for reporting performance.
- Several historical `oldname` attributes were dropped from fields.

No method signature change affecting external addons was detected beyond the renames above.

## How your habits should change

- You no longer create, rename, reorder or colour survey stages, and you cannot add your own steps: the workflow is a fixed Draft → In Progress → Closed cycle. Move a survey with the buttons on the form; the kanban view no longer changes the state.
- Editing a survey is done in a single list: "Add a page" and "Add a question" live in the same tree, and the page a question belongs to is deduced from its position. Reorganising a survey means dragging items in that list.
- If you use custom reports or templates referring to answer creation dates, use `create_date` instead of `date_create`; if you reference print/result links, use the survey URLs directly.
- Surveys sitting in an old stage must be reviewed so they land in the right new state.

## What you gain by migrating

- A simpler, more predictable survey workflow: three clear states and explicit buttons instead of a configurable pipeline nobody maintained.
- Certification surveys with scoring and time limits, and success-rate/attempt/average-score indicators readable straight from the kanban.
- A leaner data model: fewer unused fields, standard ORM dates, no page/question split to maintain, and cleaner URLs.
- Cleaner access rights, and a code base already prepared for the eLearning and appraisal features that consume surveys in 13.0.
