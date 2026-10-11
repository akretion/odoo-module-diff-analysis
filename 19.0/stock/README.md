# stock migration guide (18.0 -> 19.0)

## What's new for users

- **Packages within packages.** Put boxes on a pallet, move the pallet, and everything inside follows. Packages show a full name ("Pallet 1 > Box 12"), their content, their destination container and the transfers they belong to; "Put in Pack" can open a wizard to choose a package type or reuse an existing package.
- **Packagings merged into Units of Measure.** Packagings are no longer a separate model: they are units of measure listed on the product, and UoM categories with their "same category" restriction are gone.
- **Simpler locations.** Location types renamed (View → Virtual, Vendor Location → Vendor, …), the Comment and Corridor/Shelves/Height fields removed, fewer virtual locations created by default, and "Replenish Location" renamed "
