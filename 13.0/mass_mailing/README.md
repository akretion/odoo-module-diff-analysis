# mass_mailing migration guide (12.0 -> 13.0)

## What's new for users

- **Give a mailing a name that is not its subject.** The name is used in lists and pickers, so you can organise your mailings without changing what recipients read.
- **Mass mailing campaigns are replaced by the standard UTM Campaign.** Campaign tags and stages now live in the UTM app; there is no separate mass-mailing campaign anymore.
- **Statistics are now called "traces"** in the interface, and mailing forms show scheduled / failed / ignored traces with the reason a delivery failed.
- **The application has been reorganised** (files and views split per model, menus merged). Nothing is lost functionally, but some screen and action names differ.

Note: the 13.0 release notes also mention conversion KPIs (leads, orders, revenue) on sent emails. Those reports rely on other apps (CRM / Marketing Automation) and are **not** provided by the Community `mass_mailing` addon.

## Technical data model changes

Models renamed or removed (table names in brackets):

| 12.0 | 13.0 |
|---|---|
| mail.mail.statistics (mail_mail_statistics) | mailing.trace (mailing_trace) |
| mail.statistics.report | mail.trace.report |
| mail.mass_mailing.list | mailing.list |
| mail.mass_mailing.list.merge | mailing.list.merge |
| mail.mass_mailing.contact | mailing.contact |
| mail.mass_mailing.list_contact_rel (mail_mass_mailing_contact_list_rel) | mailing.contact.subscription (mailing_contact_list_rel) |
| mail.mass_mailing.campaign | removed — use utm.campaign |

Renamed fields:
- `link.tracker.click.mail_stat_id` -> `mailing_trace_id` (column renamed)
- `mail.mail.statistics_ids` -> `mailing_trace_ids`
- `mailing.mailing.statistics_ids` -> `mailing_trace_ids`
- `mailing.mailing.mass_mailing_campaign_id` -> removed; `campaign_id` now points to `utm.campaign`
- `mailing.trace.mass_mailing_campaign_id` -> `campaign_id` (related, stored, indexed)
- `mailing.list.subscription_contact_ids` -> `subscription_ids`
- `utm.campaign.mailing_ids` -> `mailing_mail_ids` (filtered on mail-type mailings)

Removed fields:
- `mailing.contact.is_email_valid`; contact email is no longer required (validity relies on the normalized email / blacklist mechanism)
- `mailing.contact.subscription.contact_count`

Added fields:
- `mailing.mailing.mailing_type` ('mail'; other types are added by the SMS module)
- `mailing.mailing.unique_ab_testing` (moved from the campaign to the mailing)
- `mailing.trace.trace_type`, `failure_type` (SMTP / RECIPIENT / BOUNCE / UNKNOWN) and computed `display_name`
- `utm.campaign`: `mailing_mail_ids`, `mailing_mail_count`, `mailing_items`, `mailing_clicked`, `mass_mailing_clicks_ratio`, plus mailing statistics (total, scheduled, sent, delivered, opened, replied, bounced and ratios)

Other changes: `MailingTrace.create` uses `@api.model_create_multi`; trace ordering is now `scheduled DESC`; several action methods were renamed (`action_test_mailing` -> `action_test`, `action_schedule_date` -> `action_schedule`, `put_in_queue` -> `action_put_in_queue`, `cancel_mass_mailing` -> `action_cancel`, `retry_failed_mail` -> `action_retry_failed`, `_get_convert_links` -> `_get_link_tracker_values`).

## How your habits should change

- **Campaigns** are managed on the UTM Campaign form, where mailing counters and mailing statistics are now displayed. Source and Medium are no longer carried by the campaign: set them on each mailing.
- **A/B testing**: the "Allow A/B Testing" option moved from the campaign to the mailing itself.
- **Contacts**: an email address is no longer mandatory and the "valid email" indicator is gone; use the valid-recipient filter based on the normalized email and blacklist.
- **Reporting**: look for "traces" rather than "statistics"; failed traces now carry a failure type explaining the delivery problem.
- **Customisations**: any custom view, filter, server or automated action, email template or integration referring to old names (`mail.mass_mailing.contact`, `mail.mail.statistics`, `mail.mass_mailing.campaign`, `statistics_ids`, `mail_stat_id`, …) must be updated.

## What you gain by migrating

- **One campaign object shared across Odoo** (UTM Campaign), so marketing efforts are analysed in one place instead of a mailing-only model.
- **Clearer, shorter model names** (`mailing.list`, `mailing.contact`, `mailing.trace`) that are easier to search, report on and customise.
- **Better delivery diagnosis**: failure types and a cleaner trace state, so bouncing addresses are understood instead of guessed.
- **A foundation ready for SMS mailings**, since the mailing and trace models are no longer email-only.
