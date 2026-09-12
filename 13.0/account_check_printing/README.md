# account_check_printing migration guide (12.0 -> 13.0)

This addon prints vendor checks and manages check numbering. Between 12.0 and 13.0 its scope did not change, but two things did: check numbers became text values, and the payment-registration wizard was rewritten.

## What's new for users

The official 13.0 release notes contain no dedicated entry for check printing. The Accounting chapter only announces general usability work (chatter on journals and entries, dynamic journals and ledgers, more flexible lists and search panels) and payment improvements such as split payment for partial allocation. None of it describes a new check-printing feature, so nothing Enterprise-only is presented here as a Community gain.

What you will actually notice in daily use:

- **Check numbers are now text.** They keep leading zeros and are shown exactly as printed on the paper check. The old numeric field wrongly displayed them as amounts, with a thousands separator (e.g. "1 234").
- **The journal's Next Check Number is still editable, but validated.** Only digits are accepted; otherwise Odoo refuses to save and shows "Next Check Number should only contains numbers."
- **Registering payments from invoices is simpler.** One payment is created per selected invoice, for the full residual amount. Grouping is gone, and the check number is no longer chosen at that stage: it is attributed when the check is printed.

## Technical data model changes

- `account.journal.check_next_number`: Integer -> Char (still computed, with an inverse). The inverse now validates the value with a regular expression, raises a ValidationError on non-digits, and casts it to int before writing `check_sequence_id.number_next_actual`.
- `account.payment.check_number`: Integer -> Char, readonly, copy=False, default 0. The search for the last pre-numbered check now compares with the string `"0"` and uses `int(...)` to compute the following number. A value of 0 still means "number attributed at print time".
- **The `account.register.payments` override was removed.** `AccountRegisterPayments` is replaced by `AccountPaymentRegister`, inheriting `account.payment.register`. The fields `check_amount_in_words`, `check_manual_sequencing` and `check_number` no longer exist on the wizard.
- `_onchange_journal_id` moved from the wizard to `account.payment`: when the journal uses manual sequencing, it pre-fills the check number from the journal sequence.
- `_prepare_payment_vals`: still present on the new wizard; `check_amount_in_words` is now computed from the currency found in the prepared values, instead of the wizard's currency and its removed "multi" flag.
- No method-signature change was detected by the comparison tool; the differences are in field types and model structure.

## How your habits should change

- Type only digits in Next Check Number: no spaces, dots or separators.
- When registering payments from invoices, do not look for the grouping option or a check-number field. Each invoice gets its own full-residual payment, numbered when printed. The amount can no longer be adjusted at that step.
- If you import or export check numbers (CSV, Excel, spreadsheets), treat them as text so leading zeros survive.
- If you built custom reports, templates or automated actions using `check_number` as a number, switch them to text handling.
- Everything else stays familiar: same journal options (Manual Numbering), same check printing wizard, same "Print Pre-numbered Checks" flow.

## What you gain by migrating

- Checks, stubs and reports display the real check number: no thousand separators, leading zeros preserved.
- Fewer user mistakes, since the next check number is validated before it can be saved, helping you avoid checks rejected by the bank.
- A simpler, more predictable payment registration flow, aligned with the rest of Odoo 13 accounting.
- You stay on a maintained version and benefit from the 13.0 accounting UI refresh (chatter on journals, dynamic views, customisable lists).
