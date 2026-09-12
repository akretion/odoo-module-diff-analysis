# loyalty migration guide (18.0 -> 19.0)

This guide summarises what changes for the `loyalty` addon (Loyalty Cards, Coupons, Promotions, Gift Cards, eWallet, Discount Codes) when moving a Community database from Odoo 18.0 to 19.0.

## What's new for users

- **Product domain on loyalty rules.** When you configure a conditional rule, you can now define a product domain, in addition to selecting products, categories or tags (Release notes, Sales → *Loyalty cards: product domain*). This is the natural fit when your condition is expressed by an attribute (brand, size, tag combination) rather than a fixed list of products.
- **Wallet top-up from the portal.** Customers can now add credit to their eWallet themselves from the portal instead of asking an internal user to do it (Release notes, Sales → *Wallet top-up from portal*).

The 19.0 release notes for Sales contain very little else that is specific to this addon: most of the section covers Enterprise-only features (Amazon, Shopee, Gelato, commission adjustments, quote builder, portal optional products). Do not plan a migration on the basis of those; they are not part of the Community `loyalty` addon.

## Technical data model changes

Sources: the addon diff between 18.0 and 19.0.

### No model or field added, removed or renamed

No new model and no removed model. The largest diff on the addon (PR 186624, "[CLN] {sale_}loyalty: sort and clean fields attributes") re-declares existing fields with explicit `comodel_name=` / `string=` / ordering attributes. Labels, relations, compute methods, defaults and stored values are unchanged, so **there is no data migration to run for loyalty data**: programs, rules, rewards, cards, points and coupons carry over as-is.

### Model behaviour changes

- **loyalty.card** — `_get_mail_partner()` was changed so loyalty/gift-card mail templates define a default recipient; a new `_get_mail_author()` guarantees gift card e-mails have a sender; a new `_restrict_expiration_on_loyalty()` guards the expiration date field so it can no longer be set where it is not relevant (fixes an error when adding an expiration date to a card).
- **loyalty.program** — `toggle_active()` is gone (core deprecation). Use the standard archive / unarchive actions instead.
- **loyalty.program / loyalty.reward / loyalty.rule** — `default_get(self, fields_list)` became `default_get(self, fields)`, following the core 19.0 refactor of `write()` and `default_get()` arguments.
- **product.pricelist** — new `action_archive()` override: a pricelist that is still linked to an active loyalty program can no longer be archived.
- **product.template** — new `create()` override: the gift card product is created with a placeholder, avoiding an invalid/empty product name.

## How your habits should change

- **Rule configuration:** reach for the new product domain when a list of products or a category is too coarse; existing rules keep working unchanged.
- **eWallet:** tell customers they can top up their wallet from the portal themselves; you keep the internal top-up for exceptions.
- **Archiving:** archive the loyalty program *before* its pricelist. Trying the other way round is now blocked by design.
- **Custom developments:** anything you wrote that overrides `loyalty.program.default_get()`, `loyalty.program.toggle_active()`, `loyalty.card._get_mail_partner()` or that calls `default_get(fields_list)` must be adapted before going live on 19.0.

## What you gain by migrating

- A supported branch: 19.0 remains maintained, 18.0 does not.
- Four concrete bug fixes on the addon: gift card e-mails always have a sender, setting an expiration date on a loyalty card no longer raises an error, pricelists in use by an active program can no longer be archived by mistake, and gift card products are created with a proper placeholder.
- Finer loyalty conditions through product domains, and less back-office work thanks to self-service eWallet top-ups in the portal.
- Smaller, cleaner code base: no structural change to your loyalty data, which keeps the migration short and low-risk.
