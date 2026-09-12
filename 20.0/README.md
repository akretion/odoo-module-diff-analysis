# Dude, what did they do to my Odoo at version 20.0?

You can see below the Odoo addons that got the largest data model
changes between versions 19.0 and 20.0:
(this is just summing the size of the data model impacting commits
addon per addon; method signature deltas are in each addon's
method_signatures.patch)
You can browse each directory to dig into the detail of these changes.

1. [point_of_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/point_of_sale) - 547K (15 commits)
2. [hr_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_holidays) - 267K (8 commits)
3. [stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/stock) - 242K (8 commits)
4. [mail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mail) - 191K (14 commits)
5. [mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mrp) - 174K (10 commits)
6. [base](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/base) - 150K (9 commits)
7. [resource](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/resource) - 146K (2 commits)
8. [account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/account) - 116K (8 commits)
9. [payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/payment) - 103K (3 commits)
10. [hr](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr) - 94K (10 commits)
11. [sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale) - 84K (7 commits)
12. [hr_work_entry](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_work_entry) - 81K (4 commits)
13. [pos_loyalty](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_loyalty) - 70K (1 commit)
14. [account_edi_ubl_cii](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/account_edi_ubl_cii) - 60K (1 commit)
15. [website](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/website) - 51K (4 commits)
16. [base_vat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/base_vat) - 47K (1 commit)
17. [populate](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/populate) - 45K (1 commit)
18. [product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/product) - 43K (2 commits)
19. [pos_online_payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_online_payment) - 31K (1 commit)
20. [sale_project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_project) - 30K (1 commit)
21. [sale_management](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_management) - 30K (3 commits)
22. [account_peppol](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/account_peppol) - 28K (2 commits)
23. [pos_self_order](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_self_order) - 26K (1 commit)
24. [crm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/crm) - 25K (3 commits)
25. [mass_mailing](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mass_mailing) - 25K (1 commit)
26. [im_livechat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/im_livechat) - 25K (2 commits)
27. [sale_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_timesheet) - 24K (1 commit)
28. [hr_recruitment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_recruitment) - 24K (3 commits)
29. [pos_restaurant](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_restaurant) - 23K (2 commits)
30. [maintenance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/maintenance) - 21K (3 commits)
31. [purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/purchase) - 16K (1 commit)
32. [purchase_requisition](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/purchase_requisition) - 14K (1 commit)
33. [hr_attendance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_attendance) - 13K (1 commit)
34. [sale_pdf_quote_builder](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_pdf_quote_builder) - 12K (1 commit)
35. [pos_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_sale) - 12K (1 commit)
36. [fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/fleet) - 11K (2 commits)
37. [project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/project) - 11K (2 commits)
38. [hr_fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_fleet) - 10K (3 commits)
39. [mass_mailing_sms](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mass_mailing_sms) - 9.8K (2 commits)
40. [hr_maintenance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_maintenance) - 9.3K (2 commits)
41. [crm_iap_mine](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/crm_iap_mine) - 8.6K (1 commit)
42. [sale_purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_purchase) - 8.4K (1 commit)
43. [delivery](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/delivery) - 8.4K (1 commit)
44. [event_product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/event_product) - 8.1K (1 commit)
45. [pos_bancontact_pay](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_bancontact_pay) - 7.0K (1 commit)
46. [calendar](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/calendar) - 6.9K (1 commit)
47. [lunch](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/lunch) - 6.7K (1 commit)
48. [iap](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/iap) - 6.0K (1 commit)
49. [sale_stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_stock) - 5.8K (2 commits)
50. [mail_plugin](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mail_plugin) - 5.7K (1 commit)
51. [account_payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/account_payment) - 5.5K (1 commit)
52. [survey_crm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/survey_crm) - 5.3K (1 commit)
53. [bus](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/bus) - 4.6K (1 commit)
54. [survey](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/survey) - 4.3K (1 commit)
55. [utm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/utm) - 4.1K (1 commit)
56. [pos_mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_mrp) - 4.1K (1 commit)
57. [sms](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sms) - 4.1K (1 commit)
58. [hr_presence](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_presence) - 4.0K (1 commit)
59. [purchase_product_matrix](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/purchase_product_matrix) - 3.9K (1 commit)
60. [stock_delivery](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/stock_delivery) - 3.6K (1 commit)

In total: 64 addons, 172 data model
impacting commits, 3.0M of pseudo patches.

Generated by [odoo-module-diff](https://github.com/akretion/odoo-module-diff): these numbers are
heuristic (see the repo README); the full pseudo patches are in each
addon directory.
