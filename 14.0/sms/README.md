# sms migration guide (13.0 -> 14.0)

This guide covers the `sms` addon (SMS templates and SMS sending from records). Between 13.0 and 14.0 this module is mostly a **refactoring release**: template rendering, dynamic placeholders and language detection were moved into a shared mixin of the `mail` module. What you see and do in the interface stays the same; a few private helpers disappear.

## What's new for users

The official 14.0 release notes describe SMS mainly through the **SMS Marketing** app: campaign activities separated from email ones, campaign date in Kanban, 24h recap report, testing an SMS from a contact, editing phone numbers on the fly. These relate to the marketing application (Enterprise), **not** to the Community `sms` module, so they are not gains of this upgrade.

Relevant to this addon:

- IAP/SMS: better error handling when sending an SMS if the user doesn't have a valid account — failures are reported more clearly than in 13.0.
- IAP/SMS: one SMS account can now be used across several databases.

Beyond these two items, the release notes say nothing specific about the base `sms` module. The real changes are technical and transparent for end users.

## Technical data model changes

From the commits between 13.0 and 14.0:

- `sms.template` now inherits `mail.render.mixin`.
- Removed from `sms.template` (now defined on the mixin): the placeholder-assistant fields `model_object_field`, `sub_object`, `sub_model_object_field`, `null_value`, `copyvalue`. They were `store=False`, so no column is lost and the placeholder assistant keeps working.
- Removed from `sms.template`: the `lang` field (Language). It now comes from the mixin, which is an abstract model with no table: **values previously stored in `sms_template.lang` are not migrated automatically**. Review them before upgrading if you forced a language per template.
- Methods dropped from `sms.template`: `_onchange_dynamic_placeholder`, `_build_expression`, `_get_context_lang_per_id`, `_get_ids_per_lang`, `_get_translated_bodies`.
- `mail.thread` now renders SMS templates through `template._render_field('body', self.ids, compute_lang=True)` instead of the removed language helpers.
- No other public method signature change was detected.

## How your habits should change

- Functionally, nothing: you still create SMS templates, insert placeholders and send SMS from records exactly as before.
- Placeholder insertion and language detection now use the same mechanism as email templates — what you know from one applies to the other.
- If you had forced a language directly on an SMS template in 13.0, re-check that template after migration (see above).
- For integrators: any custom module that called or overrode the removed private methods (`_get_translated_bodies`, `_build_expression`, …) must be ported to `mail.render.mixin` and `_render_field`.

## What you gain by migrating

- One shared rendering engine for SMS and email templates: consistent placeholders, consistent language handling, fewer duplicated bugs.
- A cleaner, more maintainable customization surface for partners and OCA-style modules: extend `mail.render.mixin` once instead of patching each template model.
- Clearer feedback when an SMS cannot be sent because of the IAP/SMS account, and easier account management when several databases share the same SMS credits.
- A short, low-risk upgrade path: no stored field on `sms.template` is dropped except the per-template language value, which should simply be reviewed beforehand.
