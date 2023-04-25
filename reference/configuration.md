# Configuration

This bundle can be configured under the `kreyu_data_table` key in your application configuration.

```sh
# displays the default config values defined by the bundle
$ php bin/console config:dump-reference kreyu_data_table

# displays the actual config values used by your application
$ php bin/console debug:config kreyu_data_table
```

### Themes <a href="#themes" id="themes"></a>

You can define which Twig theme to use with the data tables using the `themes` node. By default, the base theme is used. Because themes are built using [Twig blocks](https://twig.symfony.com/doc/3.x/tags/block.html), the bundle iterates through given themes, until it find the desired block, using the first one it finds.

For more information about theming, see [theming reference](https://data-table-bundle.readthedocs.io/en/latest/reference/theming/).

{% tabs %}
{% tab title="YAML" %}
{% code title="config/packages/kreyu_data_table.yaml" lineNumbers="true" %}
```yaml
kreyu_data_table:
  themes:
    - '@KreyuDataTable/themes/base.html.twig'
```
{% endcode %}
{% endtab %}

{% tab title="PHP" %}
{% code title="config/packages/kreyu_data_table.php" lineNumbers="true" %}
```php
use Symfony\Config\KreyuDataTableConfig;

return static function (KreyuDataTableConfig $config) {
    $config->themes([
        '@KreyuDataTable/themes/base.html.twig',
    ]);
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Data table builder defaults <a href="#data-table-builder-defaults" id="data-table-builder-defaults"></a>

You can specify default values applied to **all the data tables** using the `defaults` node. Those are used as a default builder values, unless the user enters some option value manually, either by passing it as a data table option, or by using the data table builder directly.

{% hint style="info" %}
The default configuration is loaded by the [DefaultConfigurationDataTableTypeExtension](https://github.com/Kreyu/data-table-bundle/blob/main/src/Extension/Core/DefaultConfigurationDataTableTypeExtension.php)
{% endhint %}

The given values below represent the default ones, unless specifically stated otherwise:

{% tabs %}
{% tab title="YAML" %}
{% code title="config/packages/kreyu_data_table.yaml" lineNumbers="true" %}
```yaml
kreyu_data_table:
  defaults:
    column_factory: kreyu_data_table.column.factory
    request_handler: kreyu_data_table.request_handler.http_foundation
    sorting:
      enabled: true
      persistence_enabled: false
      persistence_adapter: kreyu_data_table.sorting.persistence.adapter.cache # if symfony/cache is installed, null otherwise
      persistence_subject_provider: kreyu_data_table.persistence.subject_provider.token_storage # if symfony/security-bundle is installed, null otherwise
    pagination:
      enabled: true
      persistence_enabled: false
      persistence_adapter: kreyu_data_table.pagination.persistence.adapter.cache  # if symfony/cache is installed, null otherwise
      persistence_subject_provider: kreyu_data_table.persistence.subject_provider.token_storage # if symfony/security-bundle is installed, null otherwise
    filtration:
      enabled: true
      persistence_enabled: false
      persistence_adapter: kreyu_data_table.filtration.persistence.adapter.cache  # if symfony/cache is installed, null otherwise
      persistence_subject_provider: kreyu_data_table.persistence.subject_provider.token_storage # if symfony/security-bundle is installed, null otherwise
      form_factory: form.factory
      filter_factory: kreyu_data_table.filter.factory
    personalization:
      enabled: false
      persistence_enabled: false
      persistence_adapter: kreyu_data_table.personalization.persistence.adapter.cache  # if symfony/cache is installed, null otherwise
      persistence_subject_provider: kreyu_data_table.persistence.subject_provider.token_storage # if symfony/security-bundle is installed, null otherwise
      form_factory: form.factory
    exporting:
      enabled: true
      form_factory: form.factory
      exporter_factory: kreyu_data_table.exporter.factory
```
{% endcode %}
{% endtab %}

{% tab title="PHP" %}
{% code title="config/packages/kreyu_data_table.php" lineNumbers="true" %}
```php
<?php

use Symfony\Config\KreyuDataTableConfig;

return static function (KreyuDataTableConfig $config) {
    $defaults = $config->defaults();

    $defaults
        ->columnFactory('kreyu_data_table.column.factory')
        ->requestHandler('kreyu_data_table.request_handler.http_foundation')
    ;

    $defaults->sorting()
        ->enabled(true)
        ->persistenceEnabled(true)
        // if symfony/cache is installed, null otherwise
        ->persistenceAdapter('kreyu_data_table.sorting.persistence.adapter.cache')
        // if symfony/security-bundle is installed, null otherwise
        ->persistenceSubjectProvider('kreyu_data_table.persistence.subject_provider.token_storage')
    ;

    $defaults->pagination()
        ->enabled(true)
        ->persistenceEnabled(true)
        // if symfony/cache is installed, null otherwise
        ->persistenceAdapter('kreyu_data_table.pagination.persistence.adapter.cache')
        // if symfony/security-bundle is installed, null otherwise
        ->persistenceSubjectProvider('kreyu_data_table.persistence.subject_provider.token_storage')
    ;

    $defaults->filtration()
        ->enabled(true)
        ->persistenceEnabled(true)
        // if symfony/cache is installed, null otherwise
        ->persistenceAdapter('kreyu_data_table.filtration.persistence.adapter.cache')
        // if symfony/security-bundle is installed, null otherwise
        ->persistenceSubjectProvider('kreyu_data_table.persistence.subject_provider.token_storage')
    ;

    $defaults->personalization()
        ->enabled(true)
        ->persistenceEnabled(true)
        // if symfony/cache is installed, null otherwise
        ->persistenceAdapter('kreyu_data_table.personalization.persistence.adapter.cache')
        // if symfony/security-bundle is installed, null otherwise
        ->persistenceSubjectProvider('kreyu_data_table.persistence.subject_provider.token_storage')
    ;

    $defaults->exporting()
        ->enabled(true)
        ->formFactory('form.factory')
        ->exporterFactory('kreyu_data_table.exporter.factory')
    ;
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Note:** If persistence is enabled for any feature, and the [symfony/cache](https://symfony.com/doc/current/components/cache.html) is installed, then, by default, the [cache adapter](https://github.com/Kreyu/data-table-bundle/blob/main/src/Persistence/CachePersistenceAdapter.php) is used, which saves the persistence data in the cache.
{% endhint %}

{% hint style="info" %}
**Note:** If persistence is enabled for any feature, and the [symfony/security-bundle](https://symfony.com/doc/current/security.html) is installed, then, by default, the [token storage subject provider](https://github.com/Kreyu/data-table-bundle/blob/main/src/Persistence/TokenStoragePersistenceSubjectProvider.php) is used, which uses currently logged user as a persistence subject.
{% endhint %}
