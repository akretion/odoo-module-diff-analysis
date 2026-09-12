# point_of_sale migration guide (15.0 -> 16.0)

## What's new for users

- **Settings in one place.** The Point of Sale settings screen is gone: PoS configuration now lives in **General Settings**, with a selector to switch between POS configs (like websites). The list of options was cleaned up and reorganised.
- **Sensible defaults.** Limited product/partner loading and background loading are now enabled by default, so sessions start faster. Some obsolete toggles were removed and labels clarified ("Line Discounts", "Restrict Categories", "Custom Header & Footer").
- **Margins and costs are hidden by default** for everyone except the PoS manager.
- **Cash control is automatic.** "Advanced Cash Control" is no longer a checkbox: it is active as soon as the configuration has a cash payment method.
- **Opening cash counting** now happens in the POS UI with a money calculator based on the coins/bills you declare per config.
- **Closing in the POS UI.** The session closes from the POS with a summary popup (orders, payments, payment methods, cash in/out, opening notes) and, when applicable, a cash count using the same calculator.
- **Better traceability.** Opening/closing notes and cash differences are logged in the session chatter; a session now has dedicated "Opening Notes".
- **Rescue sessions** are still closed from the back end only, with the cash counted automatically, avoiding artificial cash profit/loss.

## Technical data model changes

**New model**
- `pos.bill` ("Coins/Bills"): `name`, `value`, `pos_config_ids`. Replaces the `account.bank.statement.cashbox` template formerly used as "Default Balance".

**pos.config**
- `cash_control` is now **computed** from the presence of a cash payment method (no longer a stored user setting); the constraint forbidding changes while a session is open was removed.
- Added `set_maximum_difference` (limit the tolerated closing difference).
- `default_cashbox_id` (M2o cashbox) removed → `default_bill_ids` (M2many `pos.bill`); `last_session_closing_cashbox` removed.
- Removed: `iface_orderline_customer_notes`, `iface_display_categ_images`, `allowed_pricelist_ids`, `tax_regime`, `module_account`, `product_configurator`, `selectable_categ_ids`.
- Defaults flipped to `True`: `limited_products_loading`, `product_load_background`, `limited_partners_loading`, `partner_load_background`. `is_margins_costs_accessible_to_every_user` default is now `False`.
- New helper `action_pos_config_modal_edit`.
- Many `res.config.settings` fields now mirror `pos.config` (prefix `pos_`, selector `pos_config_id`); values are written to the selected config on save.

**pos.category**
- New computed `has_image` boolean (lighter data loading in the POS).

**pos.session**
- `cash_register_id` (M2o `account.bank.statement`) **removed**. Balance start/end are now stored Monetary fields on the session: `cash_register_balance_start`, `cash_register_balance_end_real`.
- `statement_ids` (O2m `account.bank.statement`) → `statement_line_ids` (O2m `account.bank.statement.line`, with `pos_session_id`): cash control no longer depends on bank statements (optional in accounting since 16.0).
- Removed `cash_real_difference`, `cash_real_expected`; added `opening_notes`.
- New methods: `close_session_from_ui()`, `post_closing_cash_details(counted_cash)`, `get_closing_control_data()`, `update_closing_control_state_session(notes)`, `_cannot_close_session()`.
- Removed: `open_cashbox_pos()`, `_warning_balance_closing()`, model `closing.balance.confirm.wizard`.
- Signature changes: `action_pos_session_validate(balancing_account, amount_to_balance)`, `action_pos_session_close(..., bank_payment_method_diffs)`.
- `try_cash_in_out()` now creates `account.bank.statement.line` records; the closing difference is posted as a statement line using the cash journal's profit/loss accounts.
- The inherited model is now `account.bank.statement.line` (with `pos_session_id` and unlink protection) instead of `account.bank.statement`.

**pos.order**
- `invoice_group` removed (was related to config `module_account`).

## How your habits should change

- Configure your POS from **Settings > General Settings** (Point of Sale section), pick the POS in the selector, then save. The dedicated PoS settings screen no longer exists.
- Don't look for "Advanced Cash Control": add/keep a cash payment method and it applies.
- Count the opening cash and close sessions in the **POS UI**; back-end closing becomes the exception (rescue sessions, error cases).
- Declare your coins and bills on each POS config instead of maintaining a cashbox template.
- To block too-large closing differences, tick "Set Maximum Difference" and set the authorized amount; otherwise differences are accepted.
