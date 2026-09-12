# pos_online_payment migration guide (16.0 -> 17.0)

## What's new for users

`pos_online_payment` is a **new addon introduced in Odoo 17.0** — it does not exist in 16.0. This is not an upgrade of an existing feature: it is a new one you gain by migrating.

The Odoo 17 release notes cover it in one line, under Point of Sale: *"Scan and pay — Customers can now pay online by scanning a QR code."* Everything else they say about online payments (Adyen partial capture, currency filter, Stripe, tokens for public users, payment links…) belongs to the Payment application in general and is not what this addon brings. Self-order online payment is announced too, but was delivered in a separate commit, so do not count on it here.

Concretely, in Community:
- A cashier can add one or more "Online" payment lines to a POS order, alongside the usual cash/card lines, then validate.
- A popup with a QR code appears on the cashier screen **and** on the customer display. The customer scans it and pays on their own phone.
- The cashier screen refreshes itself automatically as soon as a payment succeeds.
- Several online payments can be used for one order; they are handled one after the other and the portal always shows the remaining unpaid amount.
- Before validation, the POS checks with the server whether an online payment was already made, so a cashier cannot double-charge by accident.

## Technical data model changes

**pos.payment.method** (new fields): `is_online_payment` (Boolean), `online_payment_provider_ids` (Many2many to `payment.provider`, restricted to published/enabled providers), `has_an_online_payment_provider` (computed Boolean); `type` selection gains the value `online`; new `_get_online_payment_providers()` and `_get_or_create_online_payment_method()`; `create`/`write` force journal, cash, terminal and split-transaction fields to False.

**pos.order**: `online_payment_method_id` (computed Many2one from `config_id.payment_method_ids`), `next_online_payment_amount` (Float); new `get_amount_unpaid()`, `get_and_set_online_payments_data()`, `_check_next_online_payment_amount()`, `_get_checked_next_online_payment_amount()`, `_clean_payment_lines()`, `_send_online_payments_notification_via_bus()`.

**pos.payment**: `online_account_payment_id` (Many2one to `account.payment`, one-to-one); `create`/`write` refuse an online payment without its accounting payment and block edits to essential data.

**account.payment**: `pos_order_id` + `action_view_pos_order()`.

**payment.transaction**: `pos_order_id` + `action_view_pos_order()`; overrides of `_compute_reference_prefix()`, `_set_authorized()`, `_reconcile_after_done()` and new `_process_pos_online_payment()`.

**pos.config**: `_check_online_payment_methods()` constraint, `_get_cashier_online_payment_method()`.

**pos.session**: accounting overrides `_accumulate_amounts()`, `_create_bank_payment_moves()`, `_get_split_receivable_op_vals()`, `_reconcile_account_move_lines()`; the payment method loader now sends `is_online_payment`.

No existing field is removed and no existing method signature breaks: the new code extends 16.0 behaviour.

## How your habits should change

- First, publish and enable a payment provider whose journal currency matches the POS config currency.
- Create a POS payment method, tick **Online Payment**, select the allowed providers (leave empty to allow all published ones), and add it to the POS config. **Only one online payment method per POS config is allowed.**
- Such a method has no cash journal, no terminal, no split transactions — those fields are forced off, so do not look for them.
- At the counter: add the Online payment line, validate, and let the customer scan the QR code. No physical terminal is needed for that payment.
- Closing the QR popup no longer cancels anything definite: an unpaid order stays payable through its link, and the session can still be closed normally.
- Successful online payments are read-only; the paid amount can never exceed the order total.

## What you gain by migrating

- Remote payment without extra hardware or a terminal contract per register.
- Automatic accounting: each online payment generates its own `account.payment` and `payment.transaction`, linked back to the POS order.
- Real-time cashier feedback, no polling or manual refresh.
- Safe by design: the server finalizes the order, no sensitive data travels on the bus, and payment pages are protected by the order access token.
