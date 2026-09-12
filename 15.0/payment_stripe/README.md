# payment_stripe migration guide (14.0 -> 15.0)

## What's new for users

Stripe has been rebuilt on the payment API introduced in Odoo 15. The official release notes keep a single Stripe line: **"Possibility to tokenize user cards using a redirect."** In practice:

- Customers now pay on Stripe's hosted Checkout page and are redirected back to Odoo afterwards.
- Card tokenization (saving a card for later / recurring payments) works with that redirect flow.
- The payment methods offered (card, iDEAL, Bancontact, EPS, Giropay, P24) are still filtered by the Payment Icons you linked to the acquirer, plus the customer's country, currency, and whether the card is meant to be saved.
- A new "validation" operation lets you verify a payment method with a small 1.00 validation charge, without a real sale.

The previous on-page (embedded) card form, which relied on Stripe's SetupIntent API, no longer exists.

## Technical data model changes

Code layout: `models/payment.py` is gone, replaced by `payment_acquirer.py`, `payment_token.py`, and `payment_transaction.py`. Model classes are renamed: `PaymentAcquirerStripe` → `PaymentAcquirer`, `PaymentTransactionStripe` → `PaymentTransaction`, `PaymentTokenStripe` → `PaymentToken` (they still inherit `payment.acquirer`, `payment.transaction`, `payment.token`).

Removed fields:
- `stripe_image_url` ("Checkout Image URL") on `payment.acquirer`.
- `stripe_payment_intent_secret` on `payment.transaction`.

Changed fields:
- `stripe_payment_intent` is kept, readonly.
- `stripe_payment_method` on `payment.token` is now readonly.
- `stripe_secret_key` and `stripe_webhook_secret` are now restricted to `base.group_system` (previously visible to all internal users); labels became "Secret Key", "Publishable Key", "Webhook Signing Secret".

New methods: `_get_validation_amount` (returns 1.0), `_stripe_make_request`, `_get_specific_processing_values`, `_stripe_create_checkout_session`, `_stripe_create_customer`, `_send_payment_request`, `_get_tx_from_feedback_data`, `_process_feedback_data`, `_stripe_tokenize_from_feedback_data`.

Removed methods: `stripe_form_generate_values`, `_create_stripe_session`, `_create_setup_intent`, `stripe_s2s_do_transaction`, `stripe_s2s_do_refund`, `_create_stripe_refund`, `form_feedback`, `_stripe_form_validate`, `stripe_s2s_form_process`. The diff tool reports no signature-only deltas: this is a full rewrite.

## How your habits should change

- Expect a redirect: there are no card fields inside your Odoo checkout page anymore; Stripe hosts them.
- Ask an administrator (Settings / system group) to enter or check the Stripe keys: they are no longer readable by every internal user.
- The "Checkout Image URL" setting is gone; manage your brand/logo in the Stripe dashboard instead.
- Saved cards (payment tokens) keep working: a token created with the old implementation is converted automatically the first time it is used. If Stripe returns no card for that customer, the payment raises an error ("Unable to convert payment token to new API") and the customer must re-enter a card.
- Webhooks: the signing secret is still verified, but the setting is now system-group only.
- Refunds: the Stripe-specific backend refund helpers were removed together with the direct flow. The 15.0 release notes only announce refunds for Adyen; do not assume a Stripe refund button.

## What you gain by migrating

- A supported Stripe integration aligned with Odoo 15's payment framework instead of the frozen 14.0 direct-payment code.
- Reduced PCI/compliance scope: card data never touches Odoo.
- Card saving plus recurring/off-session payments through a maintained redirect flow.
- One consistent experience across providers: unified transaction states, unified error messages, and new providers (e.g. Mollie) built on the same framework.
- Automatic conversion of existing Stripe customers and tokens, so your repeat customers do not have to re-register their cards.
