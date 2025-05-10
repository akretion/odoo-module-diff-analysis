# Dude, what did they do to my Odoo at version 19.0 (future version spoiler)?

You can see below the Odoo addons that got the largest data model changes between versions 18.0 and 19.0:
(this is just summing the size of the data model impacting commits addon per addon)
You can browse each directory to dig into the detail of these changes.

1. account - 432K
2. base - 400K
3. mail - 200K
4. hr_expense - 180K
5. point_of_sale - 160K
6. hr_holidays - 136K
7. hr_recruitment - 112K
8. stock - 96K
9. purchase - 72K
10. event - 68K
11. website - 64K
12. sale - 64K
13. product - 56K
14. base_vat - 52K
15. mrp - 48K
16. crm - 44K
17. pos_self_order - 36K
18. project - 32K
19. partner_autocomplete - 32K
20. loyalty - 32K
21. im_livechat - 32K
22. fleet - 32K
23. account_peppol - 32K
24. stock_account - 28K
25. sale_management - 28K
26. purchase_stock - 28K
27. project_timesheet_holidays - 28K
28. hr_recruitment_skills - 28K
29. hr - 28K
30. sale_expense - 24K
31. repair - 24K
32. pos_sale - 24K
33. pos_restaurant - 24K
34. mass_mailing - 24K
35. calendar - 24K
36. web_editor - 20K
37. web - 20K
38. uom - 20K
39. stock_landed_costs - 20K
40. stock_dropshipping - 20K
41. stock_delivery - 20K
42. sale_timesheet - 20K
43. sale_stock - 20K
44. sale_project - 20K
45. product_expiry - 20K
46. pos_loyalty - 20K
47. pos_hr - 20K
48. mrp_subcontracting_dropshipping - 20K
49. mrp_subcontracting - 20K
50. mrp_account - 20K
51. hr_timesheet - 20K
52. hr_fleet - 20K
53. hr_contract - 20K
54. event_crm - 20K
55. event_booth_sale - 20K
56. bus - 20K
57. survey - 16K
58. snailmail - 16K
59. sms - 16K
60. purchase_requisition - 16K
61. pos_online_payment - 16K
62. pos_event - 16K
63. portal - 16K
64. payment - 16K
65. microsoft_calendar - 16K
66. mass_mailing_sms - 16K
67. lunch - 16K
68. google_calendar - 16K
69. crm_livechat - 16K
70. auth_totp_mail - 16K

```
command:  du -sh -- */ | sort -rh | head -n 70 | awk '{sub(/\/$/, "", $2); print NR ". " $2 " - " $1}'
```
