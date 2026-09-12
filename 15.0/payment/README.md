# payment migration guide (14.0 -> 15.0)

Scope: the `payment` addon (acquirers, tokens, transactions) and its impact on accounting journals.

## What's new for users

- Payment-method management on bank journals was reworked: several payment methods can coexist on a journal, and **multiple payment acquirers can now post to the same journal**. In 14.0 each acquirer got its own auto-created journal.
- The payment link wizard now offers the acquirer as a dropdown.
- Mollie is available as a new Payment Service Provider.
- Acquirer improvements: Adyen and Buckaroo payment-error handling, partial/full refunds for Adyen, card tokenization via redirect for Stripe, ACH payments for Authorize.Net (USA only), and a cleaner in-Odoo payment flow for shoppers.

Note: PSP items ship in their own acquirer modules. Other 15.0 release notes (POS, eCommerce, most Accounting entries) do not concern this addon.

## Technical data model changes

Removed
- Inheritance of `account.chart.template` (file deleted, `_create_bank_journals` override gone).
- On `payment.acquirer`: post-init hook `create_missing_journals`, methods `_create_missing_journals`, `_get_journal_create_values`, `_get_journal_search_domain`, onchanges `_onchange_state` and `_onchange_allow_tokenization`.
- Field `account.payment.related_partner_ids` (computed Many2many).

Added
- `account.payment.suitable_payment_token_ids` (computed Many2many, display only), replacing the inline domain of `payment_token_id`.
- `account.payment.use_electronic_payment_method` (computed Boolean) deciding whether the saved-token field is shown.
- `payment.acquirer._get_default_payment_method()`.
- New `account.journal` constraint `_check_inbound_payment_method_line_ids`.

Changed
- `payment.acquirer.journal_id` is no longer stored or copied: it is computed with an inverse and its domain is now restricted to **bank** journals (cash excluded). Its value comes from the `account.payment.method.line` whose code matches the acquirer provider in the same company; setting it creates, moves or deletes that line.
- `payment_token_id` on payments now simply filters on `suitable_payment_token_ids` instead of a hardcoded domain.
- When a `payment.transaction` creates an `account.payment`, the payment method is searched on `account.payment.method` by acquirer provider instead of the hardcoded "electronic inbound" method.

The diff also holds a large "refactor of the online payment API" patch, too big to summarise here; expect provider-level API changes.

## How your habits should change

- Drop the "one journal per acquirer" habit: review your bank journals, since one journal can now serve several acquirers and carry several manual payment methods.
- Do not delete a payment-method line from a journal while an acquirer using it is enabled or in test: Odoo now blocks it with an explicit error.
- On the acquirer form the journal is a consequence of your payment-method lines, not a free choice; cash journals are no longer selectable.
- Saved-token selection is filtered by the payment method in use.
- Journals are no longer created automatically when installing an acquirer module: configure them per company.
- For payment links, select the acquirer in the wizard.

## What you gain by migrating

- Simpler accounting: fewer journals, several acquirers sharing one bank journal, easier reconciliation and reporting.
- Safer configuration thanks to the new guard on payment methods still used by enabled/test acquirers.
- A more accurate payment form: token lists filtered from the payment method context.
- Access to 15.0 PSP features (Mollie, Adyen refunds, Stripe redirect tokenization, Authorize.Net ACH) and to PSP API updates.
- A maintained version: 14.0 is end-of-life, while 15.0 still receives fixes and security patches.
