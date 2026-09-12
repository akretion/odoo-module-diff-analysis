# snailmail migration guide (12.0 -> 13.0)

Applies to the Community `snailmail` addon (physical letter sending via the Odoo IAP service).

## What's new for users

The official 13.0 release notes mention this addon once, under Usability:

> *Snailmail (IAP): Improved usability and error management when sending letters through snailmail.*

Nothing else in that section concerns snailmail: the remaining items belong to other apps, and several are Enterprise-only — do not count on them in Community.

In day-to-day terms, that single sentence means:

- **Visible failures.** A failed letter now posts a message on the source document, with a small paper-plane icon that turns red on error — the same idea as the envelope icon for failed emails.
- **A systray entry.** Letters are often sent in batches, so all documents with snailmail errors are listed in the systray, like email failures.
- **A reason for every error.** Instead of a vague "an error occurred" activity, each failure carries an explicit code: incomplete address, no credits, trial expired, destination country not supported, PDF format refused, or unknown.
- **Guided fixes.** Wizards let you complete the partner's address and resend the affected letters, buy credits, add a cover page when a PDF layout is refused, or contact support for unknown errors.
- **New cover page option.** "Add a Cover Page" can be set per company and per letter.

## Technical data model changes

**snailmail.letter**
- Removed field `activity_id` (Many2one `mail.activity`): errors no longer create an activity.
- New `error_code` (Selection: `MISSING_REQUIRED_FIELDS`, `CREDIT_ERROR`, `TRIAL_ERROR`, `NO_PRICE_AVAILABLE`, `FORMAT_ERROR`, `UNKNOWN_ERROR`, plus `ATTACHMENT_ERROR` used when the PDF cannot be generated).
- New `message_id` (Many2one `mail.message`), and the letter now stores its own address snapshot: `street`, `street2`, `zip`, `city`, `state_id`, `country_id`, copied from the partner at creation.
- New `cover` (Boolean, default taken from the company setting).
- `state`: the `draft` value is gone. States are now In Queue / Sent / Error / Canceled, with default `pending` and `required=True`.
- `create()` posts a `snailmail`-type message on the source document before creating the letter.
- `_snailmail_print` now takes an `immediate=True` argument and is split into `_snailmail_print_valid_address()` / `_snailmail_print_invalid_address()`, plus a `send_snailmail_update()` bus notification.
- `cancel()` also clears `error_code`; the `unlink()` override is removed.
- New helpers: `_snailmail_estimate_from_documents`, `fetch_failed_letters`, `_is_valid_address`, `_format_snailmail_failures`.
- The cron was rewritten: it sends pending letters and only retries `TRIAL_ERROR`, `CREDIT_ERROR`, `ATTACHMENT_ERROR` and `MISSING_REQUIRED_FIELDS`. It no longer deletes old canceled or draft letters.

**mail.message** (new inheritance)
- New fields `snailmail_error` (computed, searchable), `snailmail_status`, `letter_ids` (One2many), and the new message type `snailmail`.
- New methods `cancel_letter`, `send_letter`, `message_fetch_failed`.

**res.company / res.config.settings**
- New `snailmail_cover` setting ("Add a Cover Page"), default False, exposed in Settings.

**res.partner**
- `write()` now pushes changed address fields to letters that are not yet sent or canceled.
- `_get_address_format()` merges street and street2 on one line only when street2 is filled.

## How your habits should change

- Stop chasing snailmail activities: watch the chatter icon on the document and the systray instead.
- Always fix the cause before resending; the cron only auto-retries credit, trial, attachment and missing-address errors. Format, unsupported-country and unknown errors require your action.
- Complete the address in the partner form (or via the wizard). The wizard is offered before sending only for single documents; for batches it appears after the failure.
- There is no draft step anymore: a letter goes straight to "In Queue" and is picked up by the scheduled action.

## What you gain by migrating

- Fewer silent failures: every problem letter is traceable from its document, with a clear error code and a documented fix.
- Batch-friendly handling of errors through the systray, instead of one activity per failure.
- Stable letter data: the address used for a letter is snapshotted, and address corrections are propagated to unsent letters, avoiding rejections and wasted credits.
- The cover-page option removes a common cause of refused PDF reports, and the smarter cron retries only what can actually succeed.
