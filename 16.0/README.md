# Dude, what did they do to my Odoo at version 16.0?

You can see below the Odoo addons that got the largest data model
changes between versions 15.0 and 16.0:
(this is just summing the size of the data model impacting commits
addon per addon; method signature deltas are in each addon's
method_signatures.patch)
You can browse each directory to dig into the detail of these changes.

1. [account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/account) - 1.0M (10 commits)
2. [sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/sale) - 213K (6 commits)
3. [base](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/base) - 167K (2 commits)
4. [product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/product) - 159K (2 commits)
5. [point_of_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/point_of_sale) - 107K (3 commits)
6. [payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment) - 103K (4 commits)
7. [website](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/website) - 96K (8 commits)
8. [stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/stock) - 65K (2 commits)
9. [mail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/mail) - 56K (3 commits)
10. [im_livechat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/im_livechat) - 51K (1 commit)
11. [hr_expense](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/hr_expense) - 42K (2 commits)
12. [mass_mailing](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/mass_mailing) - 36K (3 commits)
13. [sale_management](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/sale_management) - 33K (1 commit)
14. [analytic](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/analytic) - 32K (1 commit)
15. [mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/mrp) - 30K (1 commit)
16. [account_payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/account_payment) - 24K (1 commit)
17. [event](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/event) - 23K (1 commit)
18. [microsoft_outlook](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/microsoft_outlook) - 22K (2 commits)
19. [payment_stripe](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_stripe) - 20K (1 commit)
20. [pos_loyalty](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/pos_loyalty) - 19K (1 commit)
21. [payment_adyen](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_adyen) - 18K (1 commit)
22. [survey](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/survey) - 18K (2 commits)
23. [purchase_requisition](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/purchase_requisition) - 18K (2 commits)
24. [purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/purchase) - 17K (2 commits)
25. [payment_authorize](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_authorize) - 17K (1 commit)
26. [project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/project) - 15K (2 commits)
27. [base_address_extended](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/base_address_extended) - 14K (1 commit)
28. [payment_razorpay](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_razorpay) - 13K (1 commit)
29. [stock_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/stock_account) - 13K (1 commit)
30. [payment_ogone](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_ogone) - 13K (1 commit)
31. [sale_mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/sale_mrp) - 11K (1 commit)
32. [payment_asiapay](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_asiapay) - 9.9K (1 commit)
33. [payment_payulatam](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_payulatam) - 9.4K (1 commit)
34. [payment_flutterwave](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_flutterwave) - 9.3K (1 commit)
35. [payment_sips](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_sips) - 9.1K (1 commit)
36. [payment_demo](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_demo) - 9.1K (1 commit)
37. [web_editor](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/web_editor) - 8.5K (1 commit)
38. [payment_paypal](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_paypal) - 8.3K (1 commit)
39. [payment_alipay](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_alipay) - 8.0K (1 commit)
40. [payment_custom](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_custom) - 7.7K (1 commit)
41. [payment_aps](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_aps) - 7.4K (1 commit)
42. [payment_mercado_pago](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_mercado_pago) - 7.4K (1 commit)
43. [hr_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/hr_timesheet) - 7.3K (2 commits)
44. [payment_mollie](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_mollie) - 6.9K (1 commit)
45. [payment_payumoney](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_payumoney) - 6.8K (1 commit)
46. [payment_buckaroo](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/payment_buckaroo) - 5.9K (1 commit)
47. [hr_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/hr_holidays) - 5.9K (1 commit)
48. [delivery](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/delivery) - 4.8K (1 commit)
49. [transifex](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/transifex) - 4.5K (1 commit)
50. [http_routing](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/http_routing) - 4.5K (1 commit)
51. [purchase_requisition_stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/16.0/purchase_requisition_stock) - 4.3K (1 commit)

In total: 51 addons, 91 data model
impacting commits, 2.6M of pseudo patches.

Generated by [odoo-module-diff](https://github.com/akretion/odoo-module-diff): these numbers are
heuristic (see the repo README); the full pseudo patches are in each
addon directory.
