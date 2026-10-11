# pos_online_payment migration guide (19.0 -> 20.0)

This addon lets a Point of Sale accept an online payment provider (as opposed to
cash, bank or customer account) inside the POS flow. Here is what the 19.0 to
20.0 move means for you.

## What's new for users

The 20.0 release notes about Online Payments mostly describe payment-provider
news (new providers and payment methods, tokenization, delayed confirmations,
wire transfers...). That belongs to the shared payment stack, not to this POS
addon, and it does not change how a POS online payment behaves. What matters for
you sits in the Point of Sale notes and in the POS accounting refactor:

* **End of session is simpler.** Closing a session now generates a global sale
  (and, if needed, a global refund) instead of a batch of miscellaneous entries,
  and payment journal entries are created automatically.
* **Online payments are reconciled with the session.** Each online payment is
  booked on the payment provider's receivable account, and a transfer entry now
  links it to the POS receivable used at session closing, so no manual
  reconciliation is needed.
* **Clearer configuration.** An online payment method is no longer a checkbox:
  it is a payment method *type*.

## Technical data model changes

* `pos.payment.method.is_online_payment` (Boolean) is **removed**. Online payment
  methods are now identified by `type == 'online'`. The `type` selection keeps
  its `online` value and gains `ondelete={'online': 'cascade'}`.
* Supporting members removed or renamed: `_compute_type` (it used to force the
  type from the boolean), `_is_online_payment()`, and the
  `_load_pos_data_fields` entry for `is_online_payment`;
  `_onchange_is_online_payment` becomes `_onchange_type`. `_is_write_forbidden`
  and `_force_online_payment_values` are adapted (`split_transactions` and
  `is_cash_count` no longer exist).
* `models/pos_session.py` is **deleted**. Its overrides (`_accumulate_amounts`,
  `_create_bank_payment_moves`, `_get_split_receivable_op_vals`,
  `_reconcile_account_move_lines`) are gone, replaced by a new hook on the
  payment method: `_create_payment_line(session, amount, account=None,
  message=None, partner=None)`. For `type == 'online'` it calls the new
  `_create_online_payment_line_transfer(session)`, which posts a transfer entry
  (debit POS receivable / credit payment provider receivable) and returns the
  receivable line to reconcile.
* All domains and filters move from `is_online_payment` to `type == 'online'`
  (`pos.config`, `pos.order`, `pos.payment`, `pos.payment.method`).
* Unchanged: `pos.payment.online_account_payment_id`,
  `pos.order.online_payment_method_id` and `next_online_payment_amount`.

## How your habits should change

* In **Point of Sale > Configuration > Payment Methods**, do not look for the
  "Online Payment" checkbox: set **Type = Online**. The rule "one online payment
  method per POS config" still applies, as do Allowed Providers and the provider
  currency check.
* Budget time for the **data migration**. Every payment method previously flagged
  as online must end up with `type = 'online'`. A dedicated upgrade script is
  referenced alongside the change (odoo/upgrade#10068); review it and check your
  own records after migration.
* If you maintain custom code, reports or exports reading `is_online_payment`, or
  overrides of the `pos.session` methods listed above, they must be rewritten.
* If you used payment splitting between cash and bank, note that
  `split_transactions` was removed by the POS accounting refactor; splitting is
  now limited to the customer account (`pay_later`) flow.
* Do not expect to reconcile online payments by hand any more: the transfer entry
  does it.

## What you gain by migrating

* **Cleaner accounting.** One sales move and one refunds move per session, plus a
  dedicated, automatically reconciled transfer entry for online payments.
* **Better traceability.** Each online payment keeps its link to its
  `account.payment` on the provider receivable.
* **A simpler code base.** The heavy session-level overrides disappear in favour
  of one well-scoped hook, reducing custom code and upgrade risk.
* **The same safeguards.** One online payment method per POS config, online
  payments that cannot be edited once created, and provider currency consistency.
