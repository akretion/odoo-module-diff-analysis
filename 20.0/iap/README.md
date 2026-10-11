# iap migration guide (19.0 -> 20.0)

This guide covers the `iap` addon, the Community infrastructure used to buy and track In-App Purchase credits consumed by paid Odoo services.

## What's new for users

**Nothing new in the Community `iap` module itself.** The 20.0 release notes are almost entirely about AI features (agents, chat options, skills, feedback, voice...), which are Enterprise features and are not part of this addon.

The only IAP-related statement in the release notes is that all AI features now require credits bought through IAP. That describes how those Enterprise AI features consume IAP credits, not the Odoo-side IAP configuration screens. For a Community user, the IAP account screens, credit balance, packs and warning-email settings behave as in 19.0.

The real 19.0 → 20.0 change for this addon is a removal (see below).

## Technical data model changes

Removal of the auto-refill feature (PR odoo/odoo#267128, task 6128821, upgrade script odoo/upgrade#10366).

Model `iap.account`:
- Removed field `auto_refill_threshold` (Float, default 0.0).
- Removed field `auto_refill_pack_id` (Many2one to `iap.service.pack`).
- Removed method `action_manage_payment_method()`, which returned a URL action to the IAP endpoint `/iap/1/update-auto-refill-payment-methods`.

Behaviour changes:
- `write()` no longer resets `auto_refill_threshold` / `auto_refill_pack_id` when the threshold is set to 0 or less.
- `write()` no longer calls the IAP route `/iap/1/update-auto-refill-settings`: saving an account no longer pushes auto-refill settings to the server.
- `_get_account_info()` no longer returns auto-refill values, and the periodic account refresh no longer clears or restores `auto_refill_pack_id` from server data.

No model or field was added, and no other method signature changed. The removal is breaking: these fields no longer exist after upgrade. Rationale given by Odoo: the feature was never deployed on the IAP side, and auto-refill configuration is being moved to the IAP server rather than the local database.

## How your habits should change

- If you had configured an auto-refill threshold and auto-refill pack on an IAP account, those settings disappear with the upgrade. There is no client-side replacement in 20.0: auto-refill is handled from the IAP side, not from Odoo.
- Existing values are dropped by the upgrade script; there is nothing to re-encode manually in Odoo.
- If you have custom code, views, filters, server actions or exports using `auto_refill_threshold` or `auto_refill_pack_id`, they must be cleaned up, otherwise they will fail.
- If custom code calls `action_manage_payment_method()`, remove it: payment/refill management is no longer launched from the Odoo IAP account.
- Everything else is unchanged: balance, warning threshold, warning email recipients, available packs, registration state and account linking work as before.

## What you gain by migrating

- A simpler IAP account: one half-supported, client-side setting is removed instead of drifting out of sync with the IAP server.
- Fewer outgoing RPC calls when saving IAP accounts.
- A clearer split of responsibilities: Odoo holds credits and email alerts, IAP holds refill and payment configuration.
- Access to the current IAP credit pipeline used by 20.0 paid services (including the new AI features that consume IAP credits, where you have them), with supported upgrade scripts instead of carrying dead fields.
- Low-effort migration: no new field to configure and no data entry; the work is mainly code and view cleanup for anyone who customised auto-refill.
