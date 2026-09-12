# base migration guide (12.0 -> 13.0)

## What's new for users

The official 13.0 release notes mostly cover business apps; only one line concerns `base` directly: the technical field `datas_fname` was removed from `ir.attachment` — file names now use the standard `name` field.

Everything else users will actually notice comes from the base rework:

- **Document layout wizard**: from Settings, and from the report dialog when no layout is configured, you pick the layout (Standard, Background, Boxed, Clean), logo, primary/secondary colours, font, tagline, footer and paper format — with a live preview. It replaces the old "Choose Your Document Layout" form.
- **Per-company identity**: each company gets a randomly coloured favicon, so browser tabs are easy to tell apart, plus its own font and report colours.
- **Multi-company per tab**: you explicitly choose which allowed companies to show, independently in each browser tab.
- **Better images**: contacts, users, companies and shop categories now keep one original image (up to 1920px) and derive every standard size automatically.

Warning: the PDF splitting / Documents feature of attachments was moved to the **Enterprise** `documents` module. It is **not** in Community 13.0.

## Technical data model changes

**ir.attachment** — removed fields `res_model_name`, `active`, `thumbnail`; removed methods `_make_thumbnail`, `_make_pdf`, `_split_pdf_groups`, `split_pdf`. `ir.http` gained `_get_special_models()` and an extensible url / access-token check.

**image.mixin** (new abstract model in base) — `res.partner` now inherits it. A new `fields.Image` type resizes on write. Renames: `image_original`→`image_1920`, `image_big`→`image_1024`, `image_medium`→`image_128`, `image_small`→`image_64`, plus new `image_512`/`image_256`. The generic `image` field is gone: write `image_1920`, display `image_1024`. `res.company.logo` is now related to `partner_id.image_1920`; `res.users` self-readable/writeable field lists were updated.

**Multi-company** — the `res.company` parent/child hierarchy is deprecated (fields kept for compatibility but no longer used). New context keys `allowed_company_ids` and `current_company_id`, usable in view domains and in `ir.rule` evaluation (`company_ids`); record-rule caching is now keyed on `allowed_company_ids`. `res.users._get_company()` is removed and `res.company._company_default_get()` is deprecated in favour of `self.env.company`. New `favicon`, `font`, `primary_color`, `secondary_color` on `res.company`.

**ir.actions.act_window** — removed `src_model` (use `binding_model_id`), `auto_search` and `multi`; new `binding_view_types` on `ir.actions` (default `list,form`); `binding_type` loses `action_form_only`.

**Other** — `ir.model.constraint` / `ir.model.relation`: `date_init`/`date_update` replaced by `create_date`/`write_date`; `ir.translation.source` removed (the `src` field is now kept in sync, new `_set_source()`); new `report.layout` model.

## How your habits should change

- Stop organising companies as parents/children: enable, per tab, the companies whose records you want to see. You may now invoice a sales order created in company A while logged in company B.
- Domains and record rules should use `allowed_company_ids` / `current_company_id` instead of `child_of` on the company.
- Pasting the URL of a record belonging to a company you are not in still fails: log into that company, then re-copy the link.
- Any export, template, QWeb, server action or custom module using `datas_fname`, `src_model`, `multi`, `auto_search`, `ir.attachment.active/thumbnail/res_model_name`, `image`/`image_medium`/`image_small`, `date_init`/`date_update` or `ir.translation.source` must be adapted.
- Custom code should replace `_company_default_get()` / `_get_company()` with `self.env.company`.
- If you relied on splitting PDFs from attachments, review that process: it is Enterprise-only now.

## What you gain by migrating

- Faster, cleaner images: sizes are stored instead of recomputed, cutting server CPU and speeding up shop and backend pages.
- Genuine multi-company control: per-tab company selection, distinct favicons per company, and richer access errors naming the records and the user involved.
- Consistent branded documents: layout, logo, colours, font, paper format and preview configured in one wizard.
- A more maintainable platform: deprecated helpers removed, standard `create_date`/`write_date` columns, a reusable image mixin and extensible attachment access rules — less custom code to carry through future upgrades.
