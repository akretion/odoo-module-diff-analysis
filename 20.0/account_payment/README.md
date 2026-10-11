# account_payment migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes cover this addon only lightly: payment provider views have been refined and simplified for a smoother experience. For `account_payment`, the main user-visible change is the removal of the provider `state` field (Enabled/Test/Disabled). It is replaced by two mechanisms:

- `is_live`: controls whether the provider processes transactions in its live or test environment.
- `active`: archive a provider you no longer need.

A provider is now considered available as soon as its module is installed and it is not archived. There is no separate "disabled" state.

Other Online Payments release notes entries (new providers, tokenization, specific payment methods) concern individual payment providers and are not part of this bridge addon.

## Technical data model changes

- Removed field `account.payment.method.line.payment_provider_state` (related to `payment_provider_id.state`).
- Upstream `payment.provider.state` removed; `account_payment` no longer depends on it.
- `payment.provider._compute_journal_id` dependency changed from `('code', 'state', 'company_id')` to `('code', 'is_live', 'company_id')`.
- New `payment.provider._get_journal_domain()` returns `[('company_id', '=', ...), ('type', '=', 'bank')]`.
- `_compute_journal_id` now always assigns a default bank journal when no payment method line exists; previously only for providers in `enabled`/`test`.
- Removed override `account.journal._get_available_payment_method_lines`; payment method lines are no longer filtered by provider state.
- `account.journal._unlink_except_linked_to_payment_provider` now blocks deletion whenever any provider references the journal, not only non-disabled providers. Message now says to uninstall the provider first.
- `account.payment.method.line._unlink_except_active_provider` renamed to `_unlink_except_installed_payment_provider`.
- The guard now checks `module_state != 'to remove'` instead of `state in ('enabled', 'test')`; message now says "linked to an installed provider".
- No new models or fields were added.

## How your habits should change

- Stop using Enabled/Test/Disabled. Use `is_live` for live vs test, and archive a provider when it is no longer needed.
- To retire a provider, archive it. There is no disable state to set.
- A provider can receive a journal automatically even before it is enabled. Check the journal on the provider form.
- You can no longer delete a bank journal or a payment method line while a provider module is installed and linked. Uninstall or archive/unlink the provider first.
- Payment method line views no longer show a provider state column and no longer hide lines for disabled providers. Check the provider's archive status instead.
- Journal deletion now raises the error for any linked provider, even one that previously would have been in test or disabled state.

## What you gain by migrating

- Simpler provider management: one live/test switch and archive, instead of a three-state selector.
- Fewer configuration mistakes: journals are assigned consistently, and Odoo protects journals and payment method lines still used by installed providers.
- Less hidden behavior: available payment method lines are no longer silently filtered by provider state.
- Alignment with Odoo 20's refreshed payment provider views and accounting setup.
- Cleaner upgrade path: removed `state` and `payment_provider_state` are replaced by `is_live` and `active`, which is easier to adapt in customizations, filters, and reports.
