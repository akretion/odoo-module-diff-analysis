# payment_razorpay migration guide (19.0 -> 20.0)

This guide summarizes what changes for the Razorpay payment provider when moving from Odoo 19.0 to Odoo 20.0. The scope of change in this addon is small and focused: it is a security/consistency improvement around how credentials are handled in multi-company setups. There are no changes to the customer-facing checkout flow.

## What's new for users

The official 20.0 release notes contain **no Razorpay-specific entry**. Razorpay is not mentioned in the "Online Payments" section, so there is no new Razorpay feature, no new payment method and no new authentication flow to announce for this provider.

The only broadly applicable item in the release notes is the generic payments improvement: *"The payment provider views have been refined and simplified for a smoother user experience."* This applies to payment provider configuration screens in general, including the Razorpay setup form, which is now cleaner and easier to read. Treat this as a cosmetic/UX gain, not a functional one.

Other items listed under Online Payments (new providers such as Toss Payments, PayU for India or ECPay, tokenization for ACH/Mollie/Redsys, PayPal OAuth, Wero, pricelist and amount restrictions) concern **other** providers. Some of them are Enterprise-oriented or unconfirmed for the Community edition; none of them change how Razorpay behaves. If any of them matter to your business, please confirm the edition and the exact scope with your integrator before relying on them.

In short: for a Razorpay user, 20.0 behaves exactly like 19.0 at checkout, in the customer portal, and for refunds and webhooks.

## Technical data model changes

All changes come from two related upstream improvements: `[IMP] payment_*: set copy=False on credential fields`.

**Field behavior change — credentials are no longer copied**

Every Razorpay credential/technical field on `payment.provider` now carries `copy=False`:

- `razorpay_key_id` (Razorpay Key Id)
- `razorpay_key_secret` (Razorpay Key Secret)
- `razorpay_webhook_secret` (Razorpay Webhook Secret)
- `razorpay_account_id` (Razorpay Account ID)
- `razorpay_refresh_token` (Razorpay Refresh Token)
- `razorpay_public_token` (Razorpay Public Token)
- `razorpay_access_token` (Razorpay Access Token)
- `razorpay_access_token_expiry` (Razorpay Access Token Expiry)

Previously, duplicating a company or a payment provider record could duplicate these values. From 20.0 onward, they are simply left empty on the copy.

**Provider publication behavior**

Payment providers are no longer automatically published when a new company is created. A freshly created company starts with the Razorpay provider unpublished, waiting for credentials and for an explicit decision to publish it.

**About the diff report**

The automated diff flags three fields as "deleted" and re-added: `razorpay_key_secret`, `razorpay_public_token` and `razorpay_access_token`. This is a false positive caused by the fields being reordered and re-indented inside the model class definition. **No field is actually removed.** No model is added or removed, no field is renamed, no field type changes, and there are no method signature changes in this addon.

## How your habits should change

- **Multi-company setups:** after creating a new company, you must enter the Razorpay keys again for that company. They are intentionally no longer inherited from the company you copied.
- **Provider templates:** if you duplicate a payment provider record to configure Razorpay for another company or website, remember that the keys, tokens and webhook secret are not carried over; re-enter them and re-check the webhook URL.
- **Go live checklist:** a new company's Razorpay provider is unpublished by default. Publish it yourself once the credentials are entered and tested — do not assume it is active.
- **Credentials are per company:** keep one set of Razorpay credentials per company, as Odoo explicitly assumes different companies need distinct accounts.
- **Everything else stays the same:** the same fields, the same configuration screen, the same payment and refund behavior.

## What you gain by migrating

- **Safer multi-company accounting:** credentials can no longer leak from one company into another through duplication, preventing payments from being routed to the wrong Razorpay account.
- **Fewer accidental failures:** an unpublished, unconfigured provider on a new company means no customer can hit a half-configured payment method.
- **Consistent behavior** with all other Odoo payment providers, which follow the same credential rule.
- **A low-risk migration:** no field removal, no API or signature change, no data migration script needed. Existing 19.0 credentials, transactions and tokens remain valid.
