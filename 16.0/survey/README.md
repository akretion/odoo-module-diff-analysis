# survey migration guide (15.0 -> 16.0)

This guide covers the Community `survey` addon between 15.0 and 16.0.

## What's new for users

- **Backgrounds per section.** A survey can display a different background image per section. The background fades out/in when moving to a page belonging to a section that has its own image; if a section has no image, the survey background is used.
- **Template surveys from the empty state.** The action helper shown when no survey exists now lets you load a ready-made template and start testing right away.
- **Conditional display rules follow the copy.** Copying a survey now copies its conditional display rules too.
- **Image zoom.** Participants can zoom in on images while answering.
- **Live sessions.** The end message is now displayed in live session mode.
- **Neater question forms.** Question layout is driven by rules so the form stays tidy.

Other release-note items (for example Appraisals) belong to other apps and are out of scope here.

## Technical data model changes

**New fields**
- `survey.survey.background_image_url` (Char, computed): frontend URL of the survey background.
- `survey.question.background_image` (Image, computed and stored): kept on sections only, cleared on regular questions.
- `survey.question.background_image_url` (Char, computed): section image if set, otherwise the survey image.
- `survey.question.answer.question_type` and `survey.question.answer.scoring_type` (related, read-only).

**Changed fields**
- `survey.survey.background_image`: `Binary` becomes `Image`.
- `survey.question.validation_required`: now computed and stored; it is automatically reset to `False` when the question type is not Single/Multi-line text, Numerical value, Date or Datetime.
- `survey.survey.scoring_type`: now computed and stored; enabling certification automatically sets "Scoring without answers at the end" if no scoring mode is selected.
- `survey.question.question_type`: default changed from Text Box to Multiple choice (one answer), and choice types are listed first.
- Label-only renames: Layout → Pagination, Selection → Question Selection, Percentage → Percentage left, Login Required → Require Login, Success % → Required Score (%), Is a correct answer → Correct, Score for this choice → Score, Random questions count → # Questions Randomly Picked, Email Template → Certified Email Template.

**Removed field**
- `survey.question.allow_value_image`. The answer image is now always available; leave it empty to show no image.

**Method**
- `Survey.create(vals)` becomes `create(vals_list)` with `@api.model_create_multi` (batch creation). Only relevant if you call it outside the standard ORM.

## How your habits should change

- Editing recorded answers is now reserved to Survey Managers; regular survey users keep read access on answers.
- Stop ticking "Images on answers": set or leave the image directly on each answer.
- "Validate entry" only appears on question types where it makes sense; it no longer lingers on others.
- Scoring follows certification automatically — check the value after enabling certification.
- The "Clean test answers" server action was removed: select the answers and delete them.
- Backgrounds are configured per section; leave them empty to inherit the survey background.
- Menus that were hidden in debug mode are now visible, and several options moved to a clearer place in the form.
- The answer recap on the printed page appears whenever scoring is used, not only for certifications.

## What you gain by migrating

- More engaging surveys: per-section backgrounds in both normal and live session modes.
- Faster setup: template surveys from the empty state, clearer field grouping and labelling, batch-friendly record creation.
- Cleaner data and governance: only managers can modify answers, and validation/image options are applied consistently.
- Less stale configuration: options that used to be set manually (scoring, validation, backgrounds) now follow your choices or fall back automatically.
