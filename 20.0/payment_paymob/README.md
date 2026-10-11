# payment_paymob migration guide (19.0 -> 20.0)

## What's new for users

Honestly: almost nothing changes for day-to-day users of the Paymob provider.

The official 19.0/20.0 "Online Payments" release notes describe work done on **other** payment providers (Mollie, Stripe, PayPal, Authorize.net, Redsys, and new providers such as ECPay, PayU, Toss Payments, "Pay on Invoice", plus ACH tokenization, pricelist/amount restrictions on providers, and the removal of SOFORT). **None of these items concern Paymob**, and several of them are Enterprise-only features in any case. No new Paymob payment method, no new Paymob configuration screen, and no new Paymob checkout behaviour is announced for 20.0.

The one change that a functional user can actually notice is about **company duplication and multi-company setup**: the Paymob credential fields are no longer copied from one company to another. In practice, when you duplicate a company (or create a new one in a multi-company database) that previously inherited Paymob keys, the new company's Paymob provider comes up **without credentials** and must be configured explicitly. The same commit also states the intent that payment providers should no longer be published automatically when a new company is created, precisely to avoid exposing a half-configured provider.

## Technical data model changes

Nothing was added or removed at model level: no new model, no deleted model, no new field, no deleted field. The change is a **field-behaviour change** on the existing Paymob credential fields of `payment.provider`:

- `paymob_account_country_id` — now `copy=False`
- `paymob_public_key` — now `copy=False`
- `paymob_secret_key` — now `copy=False` (still restricted to `base.group_system`)
- `paymob_hmac_key` — now `copy=False`
- `paymob_api_key` — now `copy=False`

All other attributes are unchanged: the same labels, the same `required_if_provider='paymob'` constraint, and the same access group on the secret key. The field definitions were simply reformatted onto multiple lines; the only semantic delta is `copy=False`.

No method signature change is reported for the addon, and there is no data migration script: updating the module is enough, existing credentials stored on existing providers are untouched.

## How your habits should change

- **New company, empty Paymob provider.** After duplicating a company or creating a new one, always re-enter the Paymob Public Key, Secret Key, HMAC Key and API Key (and the account country) before enabling the provider on the website or in the portal.
- **Check before publishing.** Since a provider is no longer meant to be published automatically for a new company, verify that the provider is configured *and* published in each company that should accept Paymob payments.
- **Per-company credentials are now the rule.** Treat Paymob keys as company-specific data, not as a template that can be replicated. This matches how Paymob merchant accounts actually work.
- **No action needed on existing databases.** Your current Paymob configuration in the 19.0 database carries over unchanged when you upgrade to 20.0.

## What you gain by migrating

- **Cleaner multi-company security.** Credentials can no longer leak from one company to another through duplication, which reduces the risk of charging customers through the wrong Paymob merchant account.
- **Safer onboarding of new companies.** A new company starts with an unconfigured Paymob provider, so nobody can accidentally take live payments with keys they did not set up.
- **A predictable upgrade.** The migration surface for this addon is tiny: no field to remap, no data script to run, no checkout flow to re-test beyond a standard payment smoke test.
- **Stay on the supported branch.** Upgrading keeps you aligned with the current Odoo 20.0 code base and its payment-provider framework, so future Paymob improvements and OCA-style fixes can be applied without backporting.

Bottom line: for a customer on 19.0, the Paymob addon behaves the same in 20.0 except for credential copying — the migration is low risk, and the benefit is mostly improved credential isolation across companies.
