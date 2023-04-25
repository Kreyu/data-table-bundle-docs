# Installation

## Downloading the bundle

Before you start, make sure you have [Symfony UX configured in your app](https://symfony.com/doc/current/frontend/ux.html).

```bash
$ composer require kreyu/data-table-bundle
```

## Enabling the bundle

Enable the bundle by adding it to the `config/bundles.php`:

{% code title="config/bundles.php" lineNumbers="true" %}
```php
return [
    // ...
    Kreyu\Bundle\DataTableBundle\KreyuDataTableBundle::class => ['all' => true],
];
```
{% endcode %}

## Adding the dependencies

In your `package.json` file, add a dependency that links locally to the bundle assets:

{% code title="package.json" %}
```json
{
    "devDependencies": {
        "@kreyu/data-table-bundle": "file:vendor/kreyu/data-table-bundle/assets"
    }
}
```
{% endcode %}

## Enabling the controllers

In your `assets/controller.json` file, enable the desired features controllers:

{% code title="assets/controller.json" %}
```json
{
    "controllers": {
        "@kreyu/data-table-bundle": {
            "personalization": {
                "enabled": true
            }
        }
    }
}
```
{% endcode %}

## Installing the dependencies

Using your preferred package manager, install the dependencies and build the assets:

```sh
$ yarn install && yarn build
```
