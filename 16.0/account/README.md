# account migration guide (15.0 -> 16.0)

This note covers the Odoo **account** addon (Community edition) between 15.0 and 16.0.

## What's new for users

- **Account types are now a fixed list.** The free-form "Account Type" records disappear. Each account carries a standard **Type** (Receivable, Bank and Cash, Current Assets, Current Year Earnings, Income, Expenses, Off-Balance Sheet, ...), which makes legal and country reports more reliable.
- **Payment terms got a brand new screen**: lines are edited directly (no more wizard), with new due-date computations and a live example preview showing instalments and dates. A new option "Display terms on invoice" details the deadlines and amounts on the invoice.
- **Cash discounts** were redefined on payment terms, with computation per tax legislation (included / excluded).
- **Bank**: bank statements are now optional — bank transactions can stand alone. Bank account management on partners was improved (changes logged in the chatter, manual validation of automatically added accounts).
- **Reconciliation & currencies**: an exchange-difference entry is created at each partial payment so the residual keeps a correct rate; the invoice/bill date now drives the currency conversion rate (not the accounting date).
- **Analytics**: analytic plans replace analytic groups, with an analytic widget on invoices, mass edition and reporting by plan.
- **Day-to-day comfort**: credit-limit warnings on invoices, improved reconciliation widget navigation, journal audit and dashboard alerts on non-continuous sequences, drag & drop import on dashboard cards.

## Technical data model changes

- Model `account.account.type` **removed**, along with `account.account.user_type_id`, `internal_type`, `account.account.template.user_type_id`, `account.journal.type_control_ids` and `account.move.line.account_internal_type`.
- `account.account.account_type`: new required selection (18 values). `internal_group` and `include_initial_balance` are now computed stored fields on the account itself.
- `account.move.line.account_type` is the related field to use in domains; `account_internal_group` still exists. Domains and constraints in payments, journals, taxes, products, partners and bank statement lines now filter on `account_type`.
- `account.journal.default_account_type` is now a Char (selection code) instead of a Many2one.
- `account.payment.term.line`: `option`, `day_of_the_month` and `sequence` removed; `months`, `end_month`, `days_after` added; `days` kept; default `value` changed to *Percent*; ordering by id; exactly one *Balance* line required.
- `account.payment.term`: new `display_on_invoice`, `example_amount`, `example_date`, `example_invalid`, `example_preview`. The 15.0 early-payment-discount fields (`has_early_payment`, `percentage_to_discount`, `discount_computation`, `discount_days`, `discount_account_id`...) were reverted/removed, as were the company and chart-template cash-discount write-off accounts and `account.move.invoice_early_pay_amount_after_discount`.
- Method signature changes: `account.tax._compute_taxes()` and `_prepare_tax_totals()` no longer accept an `early_payment_term` argument; `_check_rule_propositions()` on reconciliation models now takes an `account.move.line` recordset instead of a list of dicts.
- Report definitions: the per-report customisation fields (`caret_options_initializer`, `dynamic_lines_generator`, `custom_options_initializer`, `custom_line_postprocessor`, `custom_groupby_line_completer`, `custom_unfold_all_batch_data_generator`) are replaced by an abstract custom-handler model.
- Larger 16.0 changes not detailed here also touch this addon: optional bank statements, `res.bank` / `res.partner.bank` merge, foreign-currency reconciliation, unified reporting engine.

## How your habits should change

- Stop creating and maintaining "Account Type" records: set the account type directly on each account. Journal *Allowed account types* is replaced by *Allowed accounts*.
- Review every custom domain, filter, report or import template using `user_type_id` / `internal_type` — they must use `account_type`.
- Re-encode payment terms with Months / Days / End of month / Days after end of month (e.g. "30 days end of month + 10"); the old *Option* and *Day of the month* concepts no longer exist. Control existing terms after migration (single Balance line, line order).
- Early-payment-discount fields no longer exist on payment terms or on the company: use the new cash discount definition.
- Analytic groups become analytic plans.
- Custom report handlers must be moved to the new abstract handler model.

## What you gain by migrating

