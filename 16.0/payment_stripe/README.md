# payment_stripe migration guide (15.0 -> 16.0)

This addon ships in the Odoo Community edition. Between 15.0 and 16.0, `payment_stripe` follows the core payment refactoring that renames the "acquirer" concept to "provider". There is no functional rewrite of the Stripe integration — keys, tokens, webhooks and transaction handling behave the same — but a large number of technical identifiers change.

## What's new for users

From the official 16.0 release notes, the items that concern this addon:

- **Stripe: Manual Capture** — enable two-step payments: the amount is first booked on the card, then captured manually (e.g. hotel bookings).
- **Stripe: Refunds** — payments can be refunded from Odoo or from the Stripe dashboard.
- **Stripe: Country Restriction** — Stripe can no longer be configured from a country where Stripe is not supported.
- **Express Checkout** — Google Pay and Apple Pay via Stripe: billing and shipping addresses are populated automatically for a faster checkout.
- **Unpublish Providers / Filter Providers** — hide a provider from customers, or show it only below a maximum payment amount.
- **Customer Portal** — payment methods (tokens) of a disabled provider are hidden from the portal.
- **Payment tokens display** — the token list is adapted to any screen size.
- **No Invoicing/Accounting dependency** — online payment providers can now be used without installing the Invoicing/Accounting apps.

## Technical data model changes

Scope: 1 file, ~20 KB of diff, from the single upstream commit *"payment: rename of acquirer to provider"* (PR #90899), by Horacio Tellez, 2022-09-09.

**Renamed model**
- `payment.acquirer` → `payment.provider`. In this addon: Python class `PaymentAcquirer` → `PaymentProvider`, file `payment_acquirer.py` → `payment_provider.py`, import updated in `models/__init__.py`.

**Renamed fields**
- `payment.provider`: `provider` (Selection of provider codes) → `code`.
- `payment.transaction`: `provider` → `provider_code`; `acquirer_id` → `provider_id`; `acquirer_reference` → `provider_reference`.
- `payment.token`: `acquirer_id` → `provider_id`; `acquirer_ref` → `provider_ref`.

**Changed method signature**
- `_get_tx_from_notification_data(self, provider, notification_data)` → `(self, provider_code, notification_data)`.

**Other identifiers touched**
- Menu/XML id `account_payment.payment_acquirer_menu` → `payment_provider_menu` (Stripe Connect onboarding fallback).
- Connect return/refresh params `acquirer_id` → `provider_id`; user messages now say "provider" instead of "acquirer".

No model or field is added by this addon: the reported data-model match is 2 deletions / 0 additions, i.e. a pure rename. The standard upgrade scripts (odoo/upgrade#3542) carry your existing providers, tokens and transactions over to the new names.

## How your habits should change

- Menus, form titles, filters and error messages now say **Provider** instead of "Acquirer"; the Payments menu entry is **Providers**. Read the two words as synonyms.
- The Stripe configuration form itself is unchanged: same Publishable/Secret keys, same Test/Live mode, same "Connect" onboarding button. Only wording changed (e.g. *"You cannot set the provider to Test Mode while it is linked with your Stripe account."*).
- Saved filters, dashboards, exports, spreadsheet reports or automated actions built on `provider`, `acquirer_id` or `acquirer_reference` must be rebuilt with `code`, `provider_id`, `provider_reference`.
- Custom modules, server actions or QWeb templates referring to `payment.acquirer`, `payment_acquirer` paths, or the old `_get_tx_from_notification_data(provider, ...)` signature must be updated before go-live.
- Refunds become part of the regular flow: launch them from the transaction in Odoo and follow the result in the Stripe dashboard.
- If your company is registered in a country Stripe does not support, the provider can no longer be saved — a deliberate 16.0 guard rail.

## What you gain by migrating

- A vocabulary aligned with Odoo 16 documentation, support and third-party modules ("provider" everywhere): simpler configuration and troubleshooting.
- Stripe refunds and manual capture managed directly in Odoo, plus automatic address filling through Google Pay / Apple Pay express checkout.
- Better control over what customers see: unpublish a provider, filter it by amount, hide tokens of disabled providers.
- A supported release: the data migration is scripted upstream, providers/tokens/transactions and their references are preserved, and you keep receiving fixes and security patches.
