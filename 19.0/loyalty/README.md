# loyalty migration guide (18.0 -> 19.0)

## What's new for users

The 19.0 release notes say very little about Loyalty. Only two items relate to this addon, and both are safe for a Community database (loyalty is a Community addon):

- **Product domain on conditional rules.** When configuring the conditional rules of a loyalty program, you can define a product domain to decide which products actually count for the rule. It complements the existing Products / Categories / Product Tag selectors rather than replacing them.
- **Wallet top-up from the portal.** Customers using an eWallet program can add funds to their wallet themselves from the portal, instead of asking an employee to create the top-up.

Everything else in the Sales section of the release notes (catalog sections, combo products, Amazon/Shopee/Gelato, commissions, quote builder, partial payments, hide section prices, etc.) belongs to other apps and does not change Loyalty.

## Technical data model changes

Based on the data-model-impacting commits between 18.0 and 19.0:

- **No model added or removed. No field added or removed.** The only data-model commit in scope is `[CLN] {sale_}loyalty: sort and clean fields attributes` (PR 186624). Its "deleted" score is purely syntactic: declarations were rewritten field by field.
- **Declaration style only.** `fields.Many2one('res.partner', index=True)` becomes `fields.Many2one(comodel_name='res.partner', index=True)`; `string=` replaces positional labels; attributes are reordered (`string`, `comodel_name`, `compute`, `store`, `readonly`, `required`, `default`, `help`). Field names, types, labels, `required`, `default`, `ondelete`, `copy`, `compute`/`inverse`/`store` and `related` are unchanged.
- **Monetary fields** `loyalty.reward.discount_max_amount` and `loyalty.rule.minimum_amount` no longer name their currency field explicitly; they rely on the model's standard `currency_id`. Same currency, same displayed amounts.
- **Behaviour is preserved** on `loyalty.program` (`rule_ids`, `reward_ids`, `communication_plan_ids`, `coupon_ids`, `mail_template_id`, precomputed `currency_id`), `loyalty.card`, `loyalty.rule` and `loyalty.reward`. `loyalty.rule.product_domain` is present in the reviewed code and backs the rule targeting described above.
- **No method signature change** was detected in the reviewed commits. 296 lines were touched, essentially all of them cosmetic; existing records and stored values are unaffected.

## How your habits should change

- Day to day: nothing. Programs, coupons, gift cards, loyalty cards, point balances, codes, expiration dates and reward configuration stay as they are. No reconfiguration and no data migration script are required.
- If you build promotion rules, look at the product domain on conditional rules: it is more precise than long category/tag lists when only part of a category should count.
- If you use eWallet, decide whether customers may top up from the portal, grant the needed portal/website access, and tell them about it so manual top-up requests stop.
- For developers/integrators: inherit and override field declarations using the new keyword style; do not rely on positional attribute order. Diffs on loyalty models look noisy at first, but the API is identical.

## What you gain by migrating

- A functional no-op for Loyalty: migration risk and effort for this app are minimal, so you can spend the budget where real changes happened (localizations, other apps).
- A cleaner codebase: normalised field declarations are easier to read, inherit and maintain when we write your customizations or OCA-style extensions.
- Access to the 19.0 improvements listed above, plus the rest of the 19.0 standard feature set, while keeping full Community compatibility.
- Confidence: since no field or model was added or removed, your loyalty data (cards, points, rewards history) carries over as-is.
