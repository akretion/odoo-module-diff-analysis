# mass_mailing migration guide (16.0 -> 17.0)

This guide summarises what changes for Email Marketing (module `mass_mailing`) when moving from Odoo 16.0 to 17.0. It covers the Community edition only.

## What's new for users

- **Unsubscribe flow improvement.** The unsubscribe page can now be tested and customised, and a new opt-out report lets you review who opted out and why. Opt-out reasons are recorded when a contact unsubscribes from a mailing list or is added to the email blacklist, so common causes become reportable instead of being lost. Reasons can be pre-configured (with a sequence for ordering) and can be flagged as "Allow Feedback" to let the contact type a free-text explanation. Reasons chosen on blacklisted addresses are also tracked in the chatter.
- **New mailing templates.** Five mailing templates have been added to help you start a campaign faster.

Note: the official release notes sections for this app are short. Most of the 16.0 → 17.0 work is internal refactoring of subscription management; the details below come from the source changes.

## Technical data model changes

**Renamed models, tables and fields**
- Model `mailing.contact.subscription` → `mailing.subscription` (description becomes "Mailing List Subscription").
- Table `mailing_contact_list_rel` → `mailing_subscription`.
- Field `subscription_list_ids` on `mailing.contact` → `subscription_ids` (One2many to the new model).
- Field `unsubscription_date` → `opt_out_datetime` on the subscription model.

**New models / fields**
- `mailing.subscription.optout`: `name` (Reason, translated), `sequence`, `is_feedback` (Allow Feedback).
- `opt_out_reason_id` on `mailing.subscription`.
- `opt_out_reason_id` on `mail.blacklist` (tracked).
- `ab_testing_winner_mailing_id` on `utm.campaign` (Many2one to `mailing.mailing`).
- `ab_testing_is_winner_mailing` on `mailing.mailing` (computed boolean).

**Changed behaviour**
- A/B testing: the "total percentage must stay under 100%" constraint (`_check_ab_testing_total_pc`) and the `ab_testing_total_pc` field are removed. The percentage limit is replaced by a text warning, and can now legitimately reach 100% on several mailings of the same campaign (useful when mailings target different, complementary domains, since each address still receives only one email).
- The winner mailing is now explicitly stored on the campaign (`ab_testing_winner_mailing_id`) instead of being deduced from `ab_testing_pc = 100`.
- `ab_testing_completed` on `utm.campaign` is now a stored, read-only computed field derived from the winner mailing; on `mailing.mailing` it is no longer stored (still related to the campaign).
- Creating or writing `opt_out_datetime` **or** `opt_out_reason_id` now automatically sets `opt_out = True`.
- A duplicated definition of the subscription model (a leftover from a bad conflict resolution) was removed. No functional impact.

No method signature changes were detected in this module.

## How your habits should change

- When a contact unsubscribes or is blacklisted, get into the habit of picking a **reason** (and leaving feedback when the contact provides it) — this feeds the new opt-out report.
- Take a few minutes to **test and customise your Unsubscribe page** before your first campaign on 17.0.
- If you run A/B campaigns, you no longer have to keep the total below 100%. Set the winner based on the campaign's winner mailing rather than a 100% mailing.
- If you used several mailings to cover complementary recipient domains at 100%, that pattern is now officially supported.
- If you use custom exports, imports or integrations based on `mailing.contact.subscription` / `subscription_list_ids` / `unsubscription_date`, update them to the new model and field names.

## What you gain by migrating

1. **Better deliverability insight.** Opt-out reasons and the new opt-out report tell you *why* contacts leave, instead of only counting unsubscriptions.
2. **A cleaner, more flexible A/B workflow.** Removing the 100% cap unlocks the "one email per address, different domains" use case and drops a constraint that blocked legitimate campaigns.
3. **A documented, supported data model.** The subscription model is renamed to a clearer name and its table is renamed accordingly — the migration is handled by the standard upgrade scripts, so no manual data work is expected.
4. **Faster campaign creation** thanks to the new pre-built mailing templates.
5. **A more customisable unsubscribe experience**, which helps compliance and reduces list attrition.

These changes are delivered by the standard 16.0 → 17.0 upgrade path; test your campaigns on a staging copy before the production switch.
