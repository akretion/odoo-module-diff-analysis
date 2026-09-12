# base migration guide (14.0 -> 15.0)

## What's new for users

Avatars are the visible change of this release:

- Users who never uploaded a picture now get an avatar generated automatically: the initial of their name on a color derived from the record. Users are no longer all the same grey silhouette.
- Avatars are now available on many2many fields, are displayed and clickable in editable form views, and are set automatically on new many2one fields pointing to users.
- Companies, external contacts and employees follow the same logic, with a dedicated placeholder for companies.

Apart from avatars, the release notes extract describes nothing relevant for this addon — the remaining sections cover other apps and Enterprise features (Payroll, Website, Events, CRM, etc.), which are out of scope here. The other items listed below (profiling, outgoing mail) are delivered by this addon but are not described in the release notes.

## Technical data model changes

**New: `avatar.mixin` (abstract model, inherits `image.mixin`)**
- Fields `avatar_1920`, `avatar_1024`, `avatar_512`, `avatar_256`, `avatar_128`: computed images derived from the matching `image_*` field. If the image is empty, an SVG with the record's initial and a hashed color is generated, or a grey placeholder when the record has no name.
- `_avatar_name_field = "name"`.

**`res.partner`**
- `_inherit` changed from `image.mixin` to `avatar.mixin`.
- New computed `avatar_*` fields depending on `name`, `user_ids.share`, `image_*` and `is_company`. Contacts linked to internal users get initials; other contacts get a static placeholder (`company_image.png` for companies).
- `_get_placeholder_filename()` removed.

**`res.users`**
- Its own `image_1920` field (related to the partner image, with a default image) has been removed; the field now comes from the partner/avatar chain. The `_get_default_image()` method is gone.
- The `avatar_*` fields were added to the list of fields a user may read/write on their own record.
- `_get_placeholder_filename()` removed.

**New model: `ir.profile` ("Profiling results") — technical tool**
- Fields: `session`, `name`, `duration`, `init_stack_trace`, `sql`, `traces_async`, `traces_sync`, plus a computed `speedscope` binary; `_log_access = False`.
- An autovacuum method removes profiles older than 30 days.

**`ir.mail_server` (outgoing mail)**
- New field `from_filter`: the email address or domain for which a server may be used.
- `connect()` gains an `smtp_from` parameter; new helpers `_find_mail_server()`, `_match_from_filter()` and `_is_test_mode()`.
- `_prepare_email_message()` now requires the opened SMTP session, and the session is opened before the message is prepared.
- Odoo selects the server matching the sender address/domain, and rewrites the `From` header (keeping the original address in the display name) instead of spoofing it.

## How your habits should change

- You no longer need to upload a photo to tell users apart: the colored initial avatar is generated and updated automatically when the name changes. Upload a picture only if you really want a specific one.
- Avatars are clickable in form views, so you can open a user directly from the image.
- Day-to-day usage requires no action: existing records get their avatar automatically.

## What you gain by migrating

- Clearer user identification everywhere (users, contacts, employees) with zero manual data entry.
- Better email deliverability: Odoo 15 stops spoofing the `From` address and matches your outgoing server through the new "From Filter", lowering the risk of your emails being flagged as spam.
- A built-in profiler (`ir.profile`) your integrator can use to measure and document performance issues without extra tooling or modules.
- A unified image/avatar data model shared by partners and users, which simplifies custom developments and future upgrades.
