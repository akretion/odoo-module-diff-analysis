# survey migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes contain no section dedicated to the survey addon, so this guide is built from the 19.0 → 20.0 source diff of the Community `survey` module. The visible changes are a redesign of the question editor, not a rewrite of how surveys work.

**Question type picker redesigned.** The question type field no longer shows a column of radio buttons with long sentences. It is now a row of icon buttons (Single-Select, Multi-Select, Long Text, Short Text, Number, Scale, Date, Datetime...).

**Shorter, clearer type names:**

| 19.0 | 20.0 |
|---|---|
| Multiple choice: only one answer | Single-Select |
| Multiple choice: multiple answers allowed | Multi-Select |
| Multiple Lines Text Box | Long Text |
| Single Line Text Box | Short Text |
| Numerical Value | Number |

Only the labels change; the stored values behind them are the same, so existing questions keep their type after the upgrade.

**Scale questions simplified.** The "neutral" middle label disappears: the Scale Middle Label field is removed from the data model and from the question editor. Min and max label fields now come with dynamic placeholders ("Label for 0", "Label for 10") computed from your scale bounds, so an empty field tells you which end it describes.

**Editor polish.** The question title is now a large full-width heading, the description field appears on the main question view, "Suggested answers" has a placeholder, the first two Short Text answer checkboxes share one line, and the live session block is hidden when it does not apply.

## Technical data model changes

Removed field on `survey.question`: `scale_mid_label` (Char, translatable). This is the only deleted field in the diff; no model was added or removed and no new stored field was introduced. Any custom view, report, import template or Studio field using `scale_mid_label` must be adapted.

Added computed, non-stored fields on `survey.question`:
- `scale_min_label_placeholder`, computed from `scale_min`, text "Label for <min>";
- `scale_max_label_placeholder`, computed from `scale_max`, text "Label for <max>".

New methods: `_compute_scale_min_label_placeholder()` and `_compute_scale_max_label_placeholder()`, both depending on the matching scale bound. No existing method signature changed.

Renamed labels (technical keys unchanged): `save_as_nickname` becomes "Save as nickname"; the five question type labels listed above.

Data impact: existing scale middle labels are dropped during migration, with no automatic fallback to another field. If the wording matters, reword it into the min/max labels or the question description before or shortly after upgrading.

## How your habits should change

- Update your internal training material and screenshots: "Multiple choice: multiple answers allowed" is now "Multi-Select", and the other type names are shorter too.
- Stop designing a neutral middle point into scale questions. Express your scale through the minimum and maximum labels (and their new placeholders) instead.
- Administrators should audit custom views, reports, import templates and Studio fields for `scale_mid_label` before go-live, and export existing middle labels if you want to preserve the wording.
- The type picker is icon-based: hover to see which type you are picking rather than reading a full sentence.
- Leaving the min/max label fields empty is now fine — the placeholder shows the bound value, so you only type a label when it differs from the number.

## What you gain by migrating

- A faster, less cluttered question editor: one click for the type, a full-width title, description on the main view.
- Cleaner vocabulary shared with Odoo 20 documentation and demo data, which makes onboarding new survey authors easier.
- Fewer mistakes on scale questions thanks to placeholder hints tied to the actual scale bounds.
- A code base aligned with the standard 20.0 Community release: staying on 19.0 means carrying a divergent copy of the survey question views and models.
- The migration itself is small and well bounded — one removed field, two computed helpers — so the effort is mostly a review of your customizations, not a data migration project.
