# mass_mailing_sms migration guide (12.0 -> 13.0)

## What's new for users

`mass_mailing_sms` is **new in 13.0**: it does not exist in Odoo 12.0. It adds a **Mass SMS** application that sends SMS campaigns in batch through the same mailing objects as email campaigns.

- A **Notification type** on the mailing (Email or SMS), preset by the app you start from, plus a new **SMS Text Message** UTM medium.
- A new **SMS Content** tab with the SMS body, an **SMS Template**, and **Include opt-out link** (on by default) that appends a very short unsubscribe URL.
- SMS-aware statistics: sent = received, clicks still come from the link tracker, **Opened and Replied do not exist for SMS**, *bounced* = wrong number format, *exception* = any other sending issue.
- An **SMS Sent** smart button next to "Email sent" and a **Mobile** column in the trace list.
- Adapted test/send dialogs: "Send Sample SMS", recipient defaults to your name and work mobile (fallback `(123)-456-7890`), and a confirmation that the SMS will be sent to all recipients.
- Email-only fields (Subject, From, Reply to, Attachments, Mail Server, Mail Body, Opened, Replied) are hidden in SMS mode, including kanban and reporting.
- A **phone blacklist**, separate from the email blacklist. Opt-out from a list leaves that list; opt-out from a contact goes to the blacklist.
- **Insufficient credits** and other failures are surfaced on the kanban and on top of the form ("Could not be sent"), with a buy-credits action.

The 13.0 release notes contain **no section on this addon** (it is new in 13.0). SMS items listed elsewhere — delivery orders, payment follow-up, inventory notifications — belong to other apps. One adjacent change matters if you also migrate Mass Mailing: `mass_mailing.campaign` is removed in favour of `utm.campaign` (tags and stages move to UTM).

## Technical data model changes

All changes are **additions**; this addon removes nothing from 12.0.

**mailing.mailing** (extended): `mailing_type` gains `'sms'`; new fields `body_plaintext` (SMS Body), `sms_template_id` (→ `sms.template`), `sms_allow_unsubscribe` (default True), computed `sms_has_insufficient_credit`; new methods `action_put_in_queue_sms`, `action_send_sms`, `action_buy_sms_credits`, and `action_test` / `action_send_mail` / `_action_view_traces_filtered` now branch on `mailing_type`.

**mailing.trace** (extended): `trace_type` gains `'sms'`; new fields `sms_sms_id` (→ `sms.sms`), `sms_sms_id_int` (kept after the SMS is deleted), `sms_number`, `sms_code`; `failure_type` gains `sms_number_missing`, `sms_number_format`, `sms_credit`, `sms_server`, `sms_blacklist`, `sms_duplicate`; new helpers `set_sms_sent` / `_clicked` / `_ignored` / `_exception`.

**sms.sms** (extended): `mailing_id`, `mailing_trace_ids`; link shortening appends the SMS id so clicks can be traced back, and sending results are written onto the mailing traces.

**mailing.contact** (extended, also inherits `mail.thread.phone`): new `mobile` field, used by `_sms_get_number_fields`.

**mailing.list** / **link.tracker** extended for SMS recipient counting, SMS list views and shortening of text links (already-shortened and `/sms/` opt-out links are left untouched).

## How your habits should change

- SMS is a **channel on the mailing object**, not a separate module: build your campaign as usual, then choose Notification type *SMS* (or start from the Mass SMS app).
- **Metrics**: stop looking for Opened/Replied on SMS mailings; read Sent, Clicked, Bounced and Exception.
- **Contact data**: SMS reaches the `mobile` field of mailing contacts — fill it in beforehand, otherwise recipients are skipped or flagged.
- **Opt-out**: keep the opt-out link on; recipients get a short `/sms/` page to unsubscribe or blacklist their number.
- **Campaigns**: if you used Mass Mailing campaigns, `utm.campaign` replaces `mass_mailing.campaign`.
- **Credits and tests**: tests go to your own mobile number, and failed sends reveal whether you ran out of credits.

## What you gain by migrating

- Reach customers by **SMS with the same tool as email**: one contact list, one campaign/UTM model, one reporting view.
- **Trackable short links** in SMS with click statistics, plus a built-in short opt-out / blacklist page.
- **Cleaner compliance**: phone blacklist managed separately and applied automatically during mass sends.
- **Clearer errors**: wrong numbers, duplicates, blacklisted contacts, server errors and missing credits are distinguished.
- **Purely additive** for this addon: nothing from 12.0 is removed, so adoption stays optional.
