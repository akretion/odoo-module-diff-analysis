# mass_mailing migration guide (14.0 -> 15.0)

This guide covers the changes to the `mass_mailing` addon (Email Marketing) between Odoo 14.0 and 15.0, for functional users and for anyone planning a 14.0 to 15.0 upgrade.

## What's new for users

- **New email editor**: the mail composer now benefits from the features of the website builder, so you compose emails in their final rendering.
- **From Jinja to QWeb**: email templates have been ported to QWeb templates. The new composer lets you customize emails directly in their rendered version, making customizations more robust since you no longer have to edit code.
- **A/B Testing**: test multiple versions of a mailing on a sample of recipients, then send the best-performing version to the rest of the recipients.
- **Mailing Lists**: create mailing lists on the fly, and move contacts between lists using the *Add to List* button. Check the health/quality of your lists with KPIs on the kanban card.
- **Feedback**: receive feedback in the chatter after each mailing test is sent out.
- **Scheduling**: schedule and overview your mailings through a calendar view.

*(The release notes also mention SMS Marketing and Subscription-related mailings; those belong to other apps/editions and are out of scope here.)*

## Technical data model changes

**Mailing lists (`mailing.list`)**
- `contact_nbr` is replaced by `contact_count`.
- New computed statistics: `contact_count_email`, `contact_count_opt_out`, `contact_pct_opt_out`, `contact_count_blacklisted`, `contact_pct_blacklisted`, `contact_pct_bounce`.
- New `mailing_count` and `mailing_ids` (Many2many to `mailing.mailing` through `mail_mass_mailing_list_rel`).
- New actions to drill down: `action_view_contacts_email`, `action_view_mailings`, `action_view_contacts_opt_out`, `action_view_contacts_blacklisted`, `action_view_contacts_bouncing`. `action_view_contacts` no longer applies the "valid email recipient" filter automatically.

**Mailing traces (`mailing.trace`)**
- Removed fields: `ignored`, `scheduled`, `sent`, `exception`, `opened`, `replied`, `bounced`, `clicked`, `state`, `state_update`.
- New fields: `trace_status` (`outgoing`, `sent`, `open`, `reply`, `bounce`, `error`, `cancel`), `sent_datetime`, `open_datetime`, `reply_datetime`, `links_click_datetime`.
- Default ordering changed from `scheduled DESC` to `create_date DESC`.
- Status is no longer a computed field: it is written directly by the code flow, and only the latest status is kept (no full status history).
- Helper methods now take a `domain` instead of `mail_mail_ids` / `mail_message_ids`: `set_opened`, `set_replied`, `set_bounced`. New helpers: `set_sent`, `set_clicked`, `set_failed`, `set_canceled`.

**Mailings (`mailing.mailing`)**
- The `ignored` counter is renamed `canceled` (and `action_view_traces_ignored` becomes `action_view_traces_canceled`). Statistics are now based on `trace_status`.
- A/B testing reworked: `contact_ab_pc` becomes `ab_testing_pc` (default now 10%) and `unique_ab_testing` becomes `ab_testing_enabled`. New fields: `ab_testing_completed`, `ab_testing_mailings_count`, `ab_testing_schedule_datetime`, `ab_testing_winner_selection`, `ab_testing_description`. A SQL constraint enforces `ab_testing_pc` between 0 and 100.
- Campaigns (`utm.campaign`): new `ab_testing_completed`, `ab_testing_schedule_datetime`, `ab_testing_winner_selection`, `ab_testing_total_pc`, `ab_testing_mailings_count`. The total A/B percentage of a campaign must stay below 100%, and a cron automatically determines and sends the winner.

## How your habits should change

- **Email design**: you now edit emails in their final rendering in the composer. Jinja template code is replaced by QWeb, so code-level template customizations should be reviewed before/after migration.
- **Vocabulary**: "Ignored" traces are now "Canceled"; status labels become Open / Reply / Bounce. Saved filters or reports referencing the old names must be updated.
- **A/B testing** is configured per mailing, in a dedicated *A/B Test* tab (available with the mailing campaign group). Enabling it automatically creates a campaign if none is set. The sum of the A/B percentages of a campaign must remain under 100%.
- **Mailing lists**: instead of a single contact count, use the KPI buttons to jump straight to valid emails, opted-out, blacklisted or bouncing contacts, or to the mailings that used the list.

## What you gain by migrating

- **Cleaner lists, better deliverability**: opt-out, blacklist and bounce ratios are visible at a glance on the kanban card and reachable in one click.
- **A real A/B testing workflow**: send variants to a sample, compare open/click/reply rates, and let Odoo send the winner automatically on a scheduled date.
