# Changelog

## [2.0.0] - 2026-10-10
### Changed (breaking)
- **The module no longer changes Magento's CSP mode.** 1.x shipped an `etc/config.xml` that
  switched storefront and admin CSP to enforce mode and disallowed inline
  `frame-ancestors` for the whole shop. That is a shop-level decision; set it in your
  project's configuration if you want it.
### Removed
- `etc/frontend/routes.xml`, which pointed at a non-existent module (`Softcode_Whitelist`)
  and had no controllers.
### Changed
- Shared "Softcode" admin tab with the other Softcode modules; section label "CSP Whitelist".
- Composer metadata, CI on PHP 8.1–8.4, `.gitattributes` keeping tests out of dist packages.

### Earlier unreleased changes, included in 2.0.0
### Added
- **Unit tests now run in CI** as a real gate. A standalone `Test/bootstrap.php`
  autoloads the module and stubs the few mocked Magento contracts, so
  `phpunit -c phpunit.xml.dist` runs the suite without a Magento install.

### Security
- **No auto-seeded default hosts.** Removed `Helper\Data::getDefaultValues()` and the
  "seed on empty grid" behaviour in `ArraySerialized`. A security module must not
  silently broaden the CSP with example wildcards (`*.google.com` across every
  directive); an empty grid now stores an empty policy.
- **Only secure schemes.** `HostValidator` now accepts `https`/`wss` and rejects
  `http`/`ws`. A bare host (no scheme) is still allowed and follows the page scheme,
  so this does not block plain-HTTP hosts — it only refuses to *declare* insecure ones.
- **Strict host validation on save.** New `Model\HostValidator` (wired into
  `Config\Backend\ArraySerialized::beforeSave`) accepts only documented
  host-sources and rejects a bare `*`, wildcards on a bare TLD, non-host schemes
  (`data:`, `javascript:` …), keyword sources (`'unsafe-inline'` …), paths and
  unknown directives — each with a reason. A bad entry now aborts the save.
- **Canonicalisation + deduplication** of hosts per directive, on save and again
  when the policy is built (`Helper\Data`), so the emitted policy has no duplicate
  or inconsistently-cased sources.
- **Added `etc/acl.xml`** defining `Softcode_CspWhitelist::config`, the resource the
  admin section already referenced, so configuration access is properly gated.
### Added
- `Test/Unit/Model/HostValidatorTest.php` — accept/reject/canonicalise/dedupe rules
  (35 assertions across the validator).
- `Test/Unit/Helper/DataTest.php` — parsing, grouping, incomplete-row skipping,
  read-side deduplication and default seed generation.
- `.gitignore` for `/vendor/` and editor/OS artefacts.
### Removed
- Emptied the shipped `etc/csp_whitelist.xml`: dropped a large commented-out block
  of duplicated, environment-/customer-specific hosts. Hosts are managed in admin.
- 15 stray Windows `*:Zone.Identifier` files that had been committed to the repo.
- Sloppy leftover comments in `etc/adminhtml/system.xml`.

## [1.1.0]
### Changed
- Refactor plugin and helper to strict types, constructor DI and Magento coding
  standard; remove an unused dependency and add null-safe config parsing.
### Added
- composer.json metadata, MIT license, CI (PHP lint + Magento coding standard),
  and a README documenting configuration and behaviour.
