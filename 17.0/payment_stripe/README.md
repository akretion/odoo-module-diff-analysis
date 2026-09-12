# payment_stripe migration guide (16.0 -> 17.0)

## What's new for users

Odoo 17 replaces Stripe's redirect flow with a **direct payment flow**: customers now enter their payment details inside the Odoo payment form, with no redirection to Stripe's hosted Checkout page.

- **No more redirection**: the payment is confirmed on your Odoo form, so the customer stays on your site, with fewer steps and less drop-off.
- **26 additional payment methods** are supported through Stripe.
- **Indian e-mandates**: for cards issued in India, Stripe now creates an e-mandate when the card is saved, as required by the Reserve Bank of India directive. Stripe receives the mandate amount, start date, optional end date, recurrence and currency.
- **More payment methods can be saved as tokens** than before (cards or, for instance, SEPA), because the token is now built from the payment method object returned by Stripe instead of card data only.

The 17.0 release notes for Online Payments mostly describe other providers (Adyen, Razorpay, Xendit) and platform-wide payment features. For this addon, the relevant items are the three points above: direct payment flow, additional payment methods, Indian e-mandates.

## Technical data model changes

**Fields**
- `payment.transaction`: the field `stripe_payment_intent` ("Stripe Payment Intent ID") is **removed**. The Stripe intent is now identified by the standard `provider_reference` field of the transaction.
- `payment.token`: new field `stripe_mandate` ("Stripe Mandate"), readonly, storing the mandate attached to the saved payment method.

**Payment provider**
- New method `_stripe_get_inline_form_values(...)` returning a JSON payload used to render the inline (on-site) form: publishable key, currency, amount in minor units, capture method, the partner's billing details (name, email, phone, address) and tokenization information.

**Payment transaction**
- `_stripe_create_checkout_session()` and `_get_common_stripe_session_values()` are removed.
- `_stripe_create_payment_intent()` is replaced by `_stripe_create_intent()`, which creates a PaymentIntent or a SetupIntent depending on the operation.
- New `_stripe_prepare_setup_intent_payload()` and `_stripe_prepare_mandate_options()`; `_stripe_prepare_payment_intent_payload()` no longer takes the `payment_by_token` argument.
- The operation code `online_redirect` becomes `online_direct`.
- Refund requests now use the payment intent instead of the charge; capture and void also rely on `provider_reference`.
- Token creation reads the payment method from the notification data, supports non-card methods, and stores its mandate.

Overall: one field removed, one field added, no model or table added or removed.

## How your habits should change

- Stop documenting or training users on the "redirect to Stripe" step: the details are now collected in the Odoo form.
- Any saved filter, export, report or integration using the column `stripe_payment_intent` on payments must be switched to `provider_reference`.
- In the Stripe dashboard, refunds are now linked to the PaymentIntent rather than to a charge.
- When reviewing saved payment methods, check the new mandate field for Indian cards; non-card methods such as SEPA can now be tokenized too.
- Test the whole cycle after migration: online payment, manual capture, refund, void, validation (saving a card) and recurring payment by token.

## What you gain by migrating

- A modern checkout experience with no third-party redirection, which usually means fewer abandoned payments.
- A much wider choice of payment methods for your customers, without extra configuration effort.
- Compliance with the RBI directive for Indian cards, with e-mandates created automatically on the first saved card.
- Cleaner, more consistent data: the Stripe intent ID lives in the standard provider reference shared by all payment providers.
- Alignment with Stripe's current API direction (PaymentIntent / SetupIntent and the Elements API), which keeps your integration supportable and future-proof.
