# sale migration guide (17.0 -> 18.0)

## What's new for users

From the 18.0 release notes, what actually concerns the Sales app in the Community edition:

- **Quotation templates**: sequence to sort templates by importance; product descriptions can be stored on the template to pre-fill quotations.
- **Send quotation / Order confirmation** email templates now mention the product documents available to the customer on the portal.
- **Quicker quote entry**: a price you edit on an order line is no longer recomputed when the quantity changes, and product + description share a single column when editing lines.
- **Products**: Storable/Consumable become *Goods*, with a *Tracked* field; changing an attribute value updates all products; unused attribute values can be deleted.
- **Combo products** are available in Sales and eCommerce.
- **EDI for orders**: drag and drop a customer purchase order to create a pre-filled sales order when it comes from Odoo or contains the expected XML.
- **Pricelists**: views and usage were simplified.
- **Accounting side**: a company setting now defines standard sales taxes as price-included or price-excluded; analytic distribution models can be combined using sequences.

The release notes also list features that are not part of the Community edition (commission plans, PDF quote builder customisation, marketplace connectors, portal loyalty) and are therefore not covered here.

## Technical data model changes

**Removed**
- `sale.order.analytic_account_id` (Many2one to `account.analytic.account`): the account is no longer stored on the order. Sales orders now point to a **project**, whose analytic accounts (one per plan) provide order lines with their default analytic distribution.
- `s
