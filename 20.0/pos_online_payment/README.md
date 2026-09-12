# pos_online_payment migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes contain no section for this add-on, so nothing below comes from them. Everything is derived from the code changes actually shipped between 19.0 and 20.0, and all of it is available in the Community edition.

- **Online payment becomes a payment method *type*.** In 19.0 you ticked an "Online Payment" checkbox. In 20.0 you pick "Online" in the *Type* field. One setting instead of two, no more contradictory combinations.
- **A demo online payment method is created automatically** during onboarding, so a register can be tested with an online payment flow without manual setup.
- **Two online payment methods on the same register are now refused up front**, with a clear message, instead of failing at payment time.
- **Some providers (APS, Flutterwave) now require a customer on the order.** The POS flags this and asks the cashier to select a customer first.

## Technical data model changes

**Removed field:** `pos.payment.method.is_online_payment` (Boolean, "Online Payment"). Online methods are now identified only by `type == 'online'`; all domains, filters and constraints were rewritten. The `type` selection now declares `ondelete={"online": "cascade"}`.

**Removed methods.** `pos.payment.method`: `_compute_type`, `_load_pos_data_fields`, `_onchange_is_online_payment`, `_is_online_payment`, `_get_payment_terminal_selection`. The add-on's `models/pos_session.py` is deleted entirely (`_accumulate_amounts`, `_create_bank_payment_moves`, `_get_split_receivable_op_vals`, `_reconcile_account_move_lines`); that logic is absorbed by the point_of_sale accounting refactor.

**Added methods.** `pos.payment.method`: `_check_pos_config_online_payment`, `_get_provider_selection`, `_get_customer_required_providers_code` (returns `aps`, `flutterwave`), `_onchange_type`, `_create_online_payment_line_transfer(session)`, and `_create_payment_line(session, amount, account=None, message=None, partner=None, foreign_currency=None, amount_currency=None)`. `_load_pos_data_read(records, config)` replaces `_load_pos_data_fields` and exposes a `_customer_required` flag to the POS UI. `pos.config`: `_create_journal_and_payment_methods`, `_create_online_payment_demo`. `pos.order._process_order(order, existing_order)` no longer creates online payment lines on draft orders and re-syncs the partner when such a line is added.

**Accounting behaviour.** Online payments are booked on the provider's receivable account, not the POS one. At session closing the add-on now posts a transfer entry (debit POS receivable / credit provider receivable) through `_create_online_payment_line_transfer`, so online payments reconcile with the session move. The previous manual split of receivables and reconciliation is gone, and `split_transactions` is no longer force-disabled on online methods.

## How your habits should change

- Configure online payment by choosing the **Online** type. Drop the habit of ticking a separate checkbox; it no longer exists.
- Check your registers right after the migration: a POS config may have only **one** online payment method. A database that had several will now raise a validation error.
- When a payment method uses APS or Flutterwave, always select a customer on the order before taking the payment.
- Expect different session-closing entries: the core POS accounting refactor groups sales and refunds into separate moves, and online amounts appear through a dedicated transfer entry for the provider.
- If you wrote custom code on `is_online_payment` or on the POS session (`_accumulate_amounts`, `_create_bank_payment_moves`, `_get_split_receivable_op_vals`, `_reconcile_account_move_lines`), it must be ported.

## What you gain by migrating

- A single, unambiguous way to declare an online payment method — fewer configuration mistakes.
- Consistency enforced by the system: one online method per register, customer requirement for providers that need it.
- Cleaner accounting: online payments land on the right account and reconcile with the session move through an explicit transfer entry, making session closing and auditing easier.
- Faster onboarding thanks to generated demo data, and multi-currency support in the shared payment line creation logic.
- A supported version, built on the refactored point_of_sale accounting that now drives the whole closing and invoicing flow.
