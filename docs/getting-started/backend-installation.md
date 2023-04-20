# Backend installation

## Downloading the bundle

Use [Composer](https://getcomposer.org/) to install the bundle:

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
