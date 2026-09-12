# phone_validation migration guide (12.0 -> 13.0)

This addon is the low-level foundation used by Odoo to validate, format and sanitize phone numbers. In 13.0 it gained a phone blacklist and a reusable "phone" mixin used by SMS features.

## What's new for users

The official 13.0 release notes barely mention this addon directly. The VoIP items (call queue in CRM, click-to-call from a contact) are Enterprise-only features and are **not** part of the Community edition covered by this module — they are out of scope for your migration.

What the release notes do confirm is that SMS is now used throughout the business flows, for example sending an SMS automatically when a delivery order is validated. The work described here is the phone-number layer that makes those SMS flows possible: numbers must be validated and comparable before any message is sent.

Concretely, for a functional user on Community:

- A **phone blacklist** now exists, exactly like the long-standing email blacklist. Any phone number put on that blacklist will no longer receive SMS sent from Odoo, from any list or mailing.
- Contacts and other records can be flagged as blacklisted, and the blacklist can be searched/filtered from the interface.
- Phone numbers are now stored in a normalized, sanitized form, so the same number written in different ways ("+33 6 12 34 56 78", "06 12 34 56 78", with spaces or dashes) is recognized as one and the same.

## Technical data model changes

New model **`phone.blacklist`** (description "Phone Blacklist", `_rec_name = 'number'`, inherits `mail.thread`):

- `number` — Char, required, indexed, tracked; unique SQL constraint (`unique (number)`); must be E164 formatted, it is sanitized on create/write.
- `active` — Boolean, default True, tracked; deactivation is used instead of deletion.
- Helpers: `add()` / `remove()` (public, sanitize the given number) and `_add()` / `_remove()` (activating or re-activating, creating missing entries).
- `create()` sanitizes numbers, skips duplicates and returns already-existing entries instead of raising, so imports do not crash. `write()` sanitizes `number`. `_search()` greps on the sanitized value, so searching a "pretty" number finds it.

New abstract mixin **`mail.thread.phone`** ("Phone Blacklist Mixin"), inheriting `mail.thread` and `phone.validation.mixin`:

- `phone_sanitized` — Char, stored computed (`compute_sudo`), used to speed up searches and comparisons.
- `phone_blacklisted` — Boolean, computed, not stored, searchable, visible to internal users.
- API: `_phone_get_number_fields()`, `_assert_phone_field()`, `_phone_set_blacklisted()`, `_phone_reset_blacklisted()`.

Changes in **`phone.validation.mixin`**:

- New `_phone_get_country_field()` returning `country_id` when available, otherwise False.
- New `phone_get_sanitized_number(number_fname='mobile', force_format='E164')` for a single record.
- New `phone_get_sanitized_numbers(...)` returning a dictionary keyed by record id.

Also: the model loading order in `__init__.py` was reorganised, the internal tool functions were simplified and reduced, and blank-space handling in numbers was fixed. No existing public method signature was removed.

## How your habits should change

- Stop cleaning numbers by hand. Enter numbers as your users write them; Odoo now stores and compares a sanitized version for you.
- To prevent SMS, use the blacklist instead of removing the number from the contact. Blacklisting a number applies everywhere.
- Blacklisted numbers are managed from a new **Phone / SMS** menu placed under *Email* in technical settings, alongside SMS-related actions.
- Contacts flagged as blacklisted will not receive SMS mailings any more — check this field when a customer reports "no SMS received".

## What you gain by migrating

- Email-style blacklist protection for SMS, with deduplication and safe re-activation.
- Reliable number comparison and search, whatever the input format.
- A reusable mixin (`mail.thread.phone`) making any Community model SMS-ready in a few lines.
- A code base aligned with the SMS features delivered in 13.0 — a prerequisite for later upgrades.
