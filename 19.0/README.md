# Dude, what did they do to my Odoo at version 19.0?

You can see below the Odoo addons that got the largest data model
changes between versions 18.0 and 19.0:
(this is just summing the size of the data model impacting commits
addon per addon; method signature deltas are in each addon's
method_signatures.patch)
You can browse each directory to dig into the detail of these changes.

1. [account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/account) - 447K (15 commits)
2. [project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/project) - 252K (4 commits)
3. [stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/stock) - 246K (12 commits)
4. [stock_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/stock_account) - 201K (1 commit)
5. [base](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/base) - 201K (14 commits)
6. [point_of_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/point_of_sale) - 181K (9 commits)
7. [hr_expense](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_expense) - 165K (2 commits)
8. [hr](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr) - 154K (4 commits)
9. [hr_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_holidays) - 153K (6 commits)
10. [mail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/mail) - 126K (6 commits)
11. [mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/mrp) - 90K (5 commits)
12. [hr_recruitment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_recruitment) - 88K (4 commits)
13. [purchase_stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/purchase_stock) - 80K (3 commits)
14. [hr_attendance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_attendance) - 73K (4 commits)
15. [event](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/event) - 70K (3 commits)
16. [resource](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/resource) - 64K (2 commits)
17. [sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/sale) - 59K (8 commits)
18. [purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/purchase) - 55K (6 commits)
19. [hr_skills](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_skills) - 48K (2 commits)
20. [mrp_subcontracting](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/mrp_subcontracting) - 41K (1 commit)
21. [hr_recruitment_skills](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_recruitment_skills) - 37K (2 commits)
22. [base_vat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/base_vat) - 36K (1 commit)
23. [sale_management](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/sale_management) - 30K (2 commits)
24. [hr_work_entry](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_work_entry) - 27K (1 commit)
25. [fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/fleet) - 23K (2 commits)
26. [stock_landed_costs](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/stock_landed_costs) - 23K (1 commit)
27. [pos_self_order](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/pos_self_order) - 23K (2 commits)
28. [partner_autocomplete](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/partner_autocomplete) - 21K (1 commit)
29. [product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/product) - 21K (2 commits)
30. [loyalty](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/loyalty) - 21K (1 commit)
31. [hr_holidays_attendance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_holidays_attendance) - 18K (2 commits)
32. [sale_project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/sale_project) - 18K (2 commits)
33. [account_peppol](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/account_peppol) - 17K (1 commit)
34. [uom](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/uom) - 16K (1 commit)
35. [bus](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/bus) - 16K (1 commit)
36. [sale_stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/sale_stock) - 16K (1 commit)
37. [mrp_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/mrp_account) - 15K (1 commit)
38. [microsoft_outlook](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/microsoft_outlook) - 11K (1 commit)
39. [sms_twilio](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/sms_twilio) - 11K (1 commit)
40. [google_gmail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/google_gmail) - 10K (1 commit)
41. [crm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/crm) - 8.3K (2 commits)
42. [sale_expense](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/sale_expense) - 8.3K (1 commit)
43. [mrp_subcontracting_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/mrp_subcontracting_account) - 8.2K (1 commit)
44. [event_crm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/event_crm) - 8.0K (1 commit)
45. [project_timesheet_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/project_timesheet_holidays) - 7.8K (1 commit)
46. [hr_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_timesheet) - 7.5K (1 commit)
47. [im_livechat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/im_livechat) - 7.3K (1 commit)
48. [sales_team](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/sales_team) - 6.8K (1 commit)
49. [stock_fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/stock_fleet) - 6.3K (1 commit)
50. [hr_work_entry_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_work_entry_holidays) - 5.8K (1 commit)
51. [repair](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/repair) - 5.6K (1 commit)
52. [mrp_subcontracting_purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/mrp_subcontracting_purchase) - 5.3K (1 commit)
53. [hr_homeworking](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/hr_homeworking) - 5.0K (1 commit)
54. [payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/payment) - 5.0K (1 commit)
55. [crm_livechat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/crm_livechat) - 4.5K (1 commit)
56. [pos_restaurant](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/pos_restaurant) - 4.5K (1 commit)
57. [base_setup](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/base_setup) - 4.5K (1 commit)
58. [auth_totp_mail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/auth_totp_mail) - 4.4K (1 commit)
59. [product_expiry](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/product_expiry) - 3.6K (1 commit)
60. [payment_paymob](https://github.com/akretion/odoo-module-diff-analysis/blob/main/19.0/payment_paymob) - 3.0K (1 commit)

In total: 64 addons, 163 data model
impacting commits, 3.3M of pseudo patches.

Generated by [odoo-module-diff](https://github.com/akretion/odoo-module-diff): these numbers are
heuristic (see the repo README); the full pseudo patches are in each
addon directory.
