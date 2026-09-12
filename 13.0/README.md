# Dude, what did they do to my Odoo at version 13.0?

You can see below the Odoo addons that got the largest data model
changes between versions 12.0 and 13.0:
(this is just summing the size of the data model impacting commits
addon per addon; method signature deltas are in each addon's
method_signatures.patch)
You can browse each directory to dig into the detail of these changes.

1. [account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/account) - 611K (11 commits)
2. [website](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/website) - 485K (4 commits)
3. [stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/stock) - 165K (6 commits)
4. [lunch](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/lunch) - 143K (5 commits)
5. [mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/mrp) - 142K (9 commits)
6. [survey](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/survey) - 137K (6 commits)
7. [point_of_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/point_of_sale) - 129K (5 commits)
8. [product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/product) - 124K (8 commits)
9. [mass_mailing](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/mass_mailing) - 111K (6 commits)
10. [base](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/base) - 111K (12 commits)
11. [mail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/mail) - 88K (6 commits)
12. [sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/sale) - 71K (6 commits)
13. [crm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/crm) - 67K (3 commits)
14. [hr](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/hr) - 62K (6 commits)
15. [hr_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/hr_holidays) - 49K (3 commits)
16. [stock_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/stock_account) - 47K (2 commits)
17. [purchase_requisition](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/purchase_requisition) - 30K (2 commits)
18. [purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/purchase) - 27K (1 commit)
19. [purchase_stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/purchase_stock) - 27K (1 commit)
20. [mrp_subcontracting](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/mrp_subcontracting) - 26K (2 commits)
21. [mass_mailing_sms](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/mass_mailing_sms) - 23K (1 commit)
22. [hr_presence](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/hr_presence) - 23K (3 commits)
23. [snailmail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/snailmail) - 22K (1 commit)
24. [account_facturx](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/account_facturx) - 17K (1 commit)
25. [hr_attendance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/hr_attendance) - 15K (2 commits)
26. [sales_team](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/sales_team) - 15K (1 commit)
27. [phone_validation](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/phone_validation) - 14K (1 commit)
28. [microsoft_outlook](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/microsoft_outlook) - 14K (1 commit)
29. [sale_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/sale_timesheet) - 12K (1 commit)
30. [account_check_printing](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/account_check_printing) - 12K (2 commits)
31. [sale_stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/sale_stock) - 11K (1 commit)
32. [sale_coupon](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/sale_coupon) - 11K (2 commits)
33. [account_analytic_default](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/account_analytic_default) - 11K (2 commits)
34. [payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/payment) - 11K (2 commits)
35. [fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/fleet) - 10K (2 commits)
36. [pos_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/pos_sale) - 8.8K (1 commit)
37. [pos_mercury](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/pos_mercury) - 8.6K (1 commit)
38. [hr_contract](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/hr_contract) - 8.6K (1 commit)
39. [hr_skills](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/hr_skills) - 8.1K (1 commit)
40. [gamification](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/gamification) - 7.7K (2 commits)
41. [delivery](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/delivery) - 7.4K (1 commit)
42. [event](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/event) - 7.2K (1 commit)
43. [crm_iap_lead](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/crm_iap_lead) - 6.6K (2 commits)
44. [hr_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/hr_timesheet) - 6.4K (1 commit)
45. [stock_landed_costs](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/stock_landed_costs) - 6.3K (1 commit)
46. [crm_iap_lead_website](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/crm_iap_lead_website) - 5.8K (1 commit)
47. [sms](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/sms) - 5.7K (1 commit)
48. [sale_expense](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/sale_expense) - 3.9K (1 commit)
49. [im_livechat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/im_livechat) - 3.2K (1 commit)
50. [link_tracker](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/link_tracker) - 2.6K (1 commit)
51. [web](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/web) - 2.5K (1 commit)
52. [mass_mailing_event](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/mass_mailing_event) - 2.5K (1 commit)
53. [mass_mailing_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/mass_mailing_sale) - 2.0K (1 commit)
54. [payment_paypal](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/payment_paypal) - 2.0K (1 commit)
55. [mass_mailing_crm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/13.0/mass_mailing_crm) - 1.9K (1 commit)

In total: 55 addons, 149 data model
impacting commits, 2.9M of pseudo patches.

Generated by [odoo-module-diff](https://github.com/akretion/odoo-module-diff): these numbers are
heuristic (see the repo README); the full pseudo patches are in each
addon directory.
