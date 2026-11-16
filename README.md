# Gene_Braintree for OpenMage (PHP 8, 3D Secure 2 / Cardinal ready)

Braintree payments (Hosted Fields credit card, 3D Secure, PayPal, Google Pay, Apple Pay) for **OpenMage LTS 20.x** on **PHP 8.x**.

A maintained fork of the original Gene Commerce `Gene_Braintree` module (sold as "Braintree Payments With Hosted Fields" on the Magento Marketplace). It builds on [justinbeaty/module-gene-braintree](https://github.com/justinbeaty/module-gene-braintree), which moved the module to `braintree/braintree_php` 6.x via Composer.

This fork runs in production on a multi-store OpenMage 20.18 / PHP 8.4 shop.

## What this fork adds

- **Braintree Web SDK 3.147.0.** Braintree requires Web SDK **3.121.0 or newer** for its new Cardinal 3D Secure integration. The deadline is **28 February 2027**, and older SDKs may fail 3DS authentication.
- **3D Secure fix for newer SDKs.** `threeDSecure.verifyCard()` now requires the card BIN. Without it, checkout fails with `THREEDS_MISSING_VERIFY_CARD_OPTION`. The BIN is now passed for new cards (from Hosted Fields tokenization) and for vaulted cards (`data-threedsecure-bin`).
- **PHP 8 / OpenMage 20 fixes:**
  - `implode()` argument order in the payment info templates
  - `_isAllowed(): bool` in the admin controllers
  - string-cast config values for `braintree_php` 6.x

## Installation

```sh
composer require degriz/openmage-braintree
```

The package `replace`s `gene/braintree`, so it takes over from the old package. Your `composer.json` needs the Magento installer:

```json
{
  "require": {
    "degriz/openmage-braintree": "^3.2",
    "magento-hackathon/magento-composer-installer": "^4.0"
  },
  "extra": {
    "magento-root-dir": ".",
    "magento-deploystrategy": "copy",
    "magento-force": true
  },
  "config": {
    "allow-plugins": {
      "magento-hackathon/magento-composer-installer": true
    }
  }
}
```

**Switching from `gene/braintree` (justinbeaty's repository):** remove `gene/braintree` from `require` and its `vcs` entry from `repositories`, then run `composer require degriz/openmage-braintree --no-dev`. Any `magento-deploy-ignore` entries you kept for local PHP 8 patches of this module can be removed, because those fixes are included here.

After a `composer update`, check that the files in `vendor/gene/braintree` were actually copied into the Magento root. Flush the Magento cache and OPcache afterwards.

## Content Security Policy

If your shop sends a CSP header, add the new Cardinal domains and keep your existing `cardinalcommerce.com` entries:

| Environment | Directive     | Domain                               |
|-------------|---------------|--------------------------------------|
| Production  | `script-src`  | `static.client.cardinaltrusted.com`  |
| Production  | `connect-src` | `*.cardinaltrusted.com`              |
| Sandbox     | `script-src`  | `cas.static.client.cardinaltrusted.com` |
| Sandbox     | `connect-src` | `*.cardinaltrusted.com`              |

## Upgrading the SDK version

The Web SDK version is a single constant: `SDK_VERSION` in `app/code/community/Gene/Braintree/Block/Assets.php`. Stay on the 3.x line. Braintree Web SDK v4 is still alpha.

## Credits

- Original module: Gene Commerce
- Composer / braintree_php 6.x port: [Justin Beaty](https://github.com/justinbeaty/module-gene-braintree)
- 3DS / SDK 3.147 and PHP 8 fixes: [Degriz](https://www.degriz.net)

Test on a staging site before going live.
