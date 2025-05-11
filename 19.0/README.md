# Dude, what did they do to my Odoo at version 19.0 (future version spoiler)?

You can see below the Odoo addons that got the largest data model changes between versions 18.0 and 19.0:
(this is just summing the size of the data model impacting commits addon per addon)
You can browse each directory to dig into the detail of these changes.

1. account - 380K
2. base - 304K
3. hr_expense - 164K
4. mail - 132K
5. point_of_sale - 112K
6. hr_holidays - 112K
7. hr_recruitment - 88K
8. stock - 64K
9. purchase - 56K
10. event - 52K
11. sale - 48K
12. base_vat - 44K
13. fleet - 32K
14. product - 28K
15. partner_autocomplete - 28K
16. loyalty - 28K
17. hr_recruitment_skills - 24K
18. account_peppol - 24K
19. uom - 20K
20. pos_self_order - 20K
21. mrp - 20K
22. bus - 20K
23. website - 16K
24. sale_expense - 16K
25. event_crm - 16K
26. crm - 16K
27. sales_team - 12K
28. sale_management - 12K
29. project_timesheet_holidays - 12K
30. project - 12K
31. pos_restaurant - 12K
32. im_livechat - 12K
33. crm_livechat - 12K
34. base_setup - 12K
35. auth_totp_mail - 12K
36. sale_async_emails - 7.5K
37. pos_sale - 7.5K
38. pos_discount - 7.5K

```
command:  du -sh -- */ | sort -rh | head -n 70 | awk '{sub(/\/$/, "", $2); print NR ". " $2 " - " $1}'
```
