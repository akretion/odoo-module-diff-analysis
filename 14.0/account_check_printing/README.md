# account_check_printing migration guide (13.0 -> 14.0)

Scope: the Community addon handling check payments. The 14.0 release notes cover Accounting broadly; only the payment-related items apply to this addon, and the details below come from the addon's own changes.

## What's new for users

* **Payments go through outstanding accounts.** A check payment creates its journal entry immediately on the journal's outstanding payment account; the invoice becomes *Paid* only once the entry is reconciled with the bank statement line.
* **Payment follow-up is tracked.** The print/sent status is stored on the payment and logged in the chatter. A new **Void Check** button cancels a check in one click (draft + cancel).
* **Preferred payment method per vendor.** Defined on the vendor (or visible on the vendor bill), Odoo pre-selects it when registering an outbound payment, making check runs and grouped payments faster.
* **Cleaner check numbers.** Numbers are stored as text, so `000012345` is no longer displayed as `12,345`; the sequence padding follows the number you enter.
* **Amount in words is computed and read-only**, so it can no longer mismatch the amount in digits.
* **Safety checks.** The same check number cannot be used twice in a journal, numbers must be digits only, and cash journals no longer offer the check payment method.

## Technical data model changes

`account.payment`:
* `check_amount_in_words`: stored computed field (`_compute_check_amount_in_words`), no longer editable.
* `check_number`: stored, read-only, computed (`_compute_check_number`) with a new `_inverse_check_number` and a `_constrains_check_number` constraint.
* `check_number_int`: **removed**; increments/comparisons use SQL `check_number::INTEGER`.
* The override on `account.payment.register` (and its `check_amount_in_words`) is removed.
* Renames: `post` -> `action_post`, `unmark_sent` -> `action_unmark_sent`, `set_check_amount_in_words` **removed**. `do_print_checks` writes `is_move_sent` instead of `state='sent'` and raises a `RedirectWarning` when no layout is set.
* Print data renamed: `communication` -> `ref`, `payment_date` -> `date`, `reconciled_invoice_ids` -> `move_id._get_reconciled_invoices()`, `move_line_ids` -> `line_ids`.

`account.journal`:
* `check_next_number` now uses the sequence `get_next_char` and its inverse stores the padding.
* The `account.chart.template` inherit is **removed**; the check method is excluded from cash journals by `_compute_outbound_payment_method_ids`.

New:
* `account.move.preferred_payment_method_id` (stored computed from the partner).
* `res.partner.property_payment_method_id` (company-dependent), used by `account.payment._compute_payment_method_id` as default for outbound payments.

## How your habits should change

* Do not type the amount in words anymore: it is filled automatically. Correct the amount instead.
* Check numbers are text: enter them as printed, leading zeros are kept and the sequence keeps the same width.
* The same check number cannot be reused in a journal; Odoo blocks it and lists the conflicting numbers.
* The manual "sent" state is gone: printing marks the payment as sent (chatter), and you clear it with *Unmark Sent*.
* Check printing is not available on cash journals - use a bank journal.
* Use **Void Check** instead of manually cancelling a check payment.
* When no check layout is configured, Odoo offers a button to open the configuration panel directly.

## What you gain by migrating

* Fewer errors: amount in words and check numbering are computed and validated for you.
* No duplicate or re-used check numbers, and check numbering stays aligned with your pre-printed paper, including leading zeros.
* Clear audit trail of who printed, sent or voided a payment, and correct "Paid" statuses driven by real bank reconciliation.
* Faster check runs thanks to the vendor's preferred payment method.
* You stay on a supported version and on the payment/reconciliation workflow that Odoo 14 and later build on.
