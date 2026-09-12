# payment_razorpay migration guide (15.0 -> 16.0)

This addon lets you accept Razorpay payments (India) on Odoo sales, invoices and eCommerce checkout. Between 15.0 and 16.0 the Razorpay feature set itself is stable: the real change is a global renaming of Odoo's payment core, where the term **"acquirer" became "provider"**. For you as a user, that mostly means renamed menus and labels; for customizations and integrations, it means renamed models, fields and methods.

## What's new for users

The 16.0 release notes barely mention Razorpay beyond confirming it as the online payment provider for India. However, several payment-wide improvements in 16.0 also apply here:

- Online payment providers can now be used **without installing the Invoicing/Accounting apps** — useful if you only take payments on the website/eCommerce.
- You can now **unpublish a provider** so it is hidden from customers without deleting its configuration.
- Providers can be **filtered by maximum payment amount**, so a Razorpay rule can be shown only where it makes sense.
- **Payment tokens**: tokens belonging to disabled providers are hidden from the customer portal, and the token display is now responsive on any screen size.

Razorpay keeps the behaviour you know from 15.0: manual capture (authorize now, capture later), partial refunds from Odoo, and the same currency restriction (unsupported currencies filter Razorpay out of checkout). The 16.0 manual-capture announcements in the release notes concern Adyen and Stripe; they are not a Razorpay change.

## Technical data model changes

The only 15.0 -> 16.0 patch for this addon (`odoo/odoo#90899`) is the acquirer-to-provider rename. Changes affecting the data model:

- The model **`payment.acquirer` is renamed `payment.provider`**, and the addon's file/class follow (`payment_acquirer.py` -> `payment_provider.py`, `PaymentAcquirer` -> `PaymentProvider`, `_inherit = 'payment.provider'`).
- On that model, the selection field **`provider` is renamed `code`** (Razorpay's value stays `'razorpay'`).
- On **`payment.transaction`**: `acquirer_id` -> `provider_id`, `acquirer_reference` -> `provider_reference`, and the `provider` selection field -> `provider_code`.
- Method signature deltas:
  - `_get_compatible_acquirers(...)` -> `_get_compatible_providers(...)`
  - `_get_tx_from_notification_data(self, provider, notification_data)` -> `(self, provider_code, notification_data)`
  - `_create_refund_transaction(acquirer_reference=...)` -> `provider_reference=...`
- Razorpay's own configuration fields are **unchanged**: `razorpay_key_id`, `razorpay_key_secret`, and the manual-capture/refund feature flags.

No model or field is added or removed by this addon: the change is a pure rename. The stored data is carried over by Odoo's standard upgrade scripts (referenced as `odoo/upgrade#3542`).

## How your habits should change

- In the UI, "Payment Acquirers" is now **Payment Providers** (Settings > Payment Providers). Your existing Razorpay record, keys, journal and payment flows are preserved — no reconfiguration is needed.
- Any **saved filters, favourites, automated actions, exports/imports or Studio fields** referring to `payment.acquirer`, `acquirer_id`, `acquirer_reference` or the transaction `provider` column must be renamed.
- Any **custom module** that inherits or XPaths the payment views, or calls the old method names, must be updated to the new provider terminology.
- **External reporting** (SQL views, BI/dashboards) querying the old table or column names must be updated, otherwise reports will break after the migration.
- Everything you do day to day on Razorpay checkouts, captures and refunds works exactly as before.

## What you gain by migrating

- You stay on a supported Odoo version, with a **clean, factually correct terminology** ("provider") used consistently across payments.
- You can use Razorpay **without installing Accounting/Invoicing**, simplifying deployments that only need online payments.
- You benefit from the 16.0 payment-core polish: unpublishing providers, amount-based provider filtering, cleaner token display and portal behaviour.
- Your Razorpay integration keeps manual capture, partial refunds and webhook-based status handling, while gaining the standard upgrade path for future versions.
- Customization effort is limited and predictable: it is a rename exercise, not a functional rewrite.
