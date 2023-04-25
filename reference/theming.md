# Theming

Every HTML part of this bundle can be customized using [Twig](https://twig.symfony.com/) themes.

## Built-in themes

The following themes are natively available in the bundle:

* [@KreyuDataTable/themes/bootstrap\_5.html.twig](https://github.com/Kreyu/data-table-bundle/blob/main/src/Resources/views/themes/bootstrap\_5.html.twig) - integrates [Bootstrap 5](https://getbootstrap.com/docs/5.0/);
* [@KreyuDataTable/themes/tabler.html.twig](https://github.com/Kreyu/data-table-bundle/blob/main/src/Resources/views/themes/tabler.html.twig) - integrates [Tabler UI Kit](https://tabler.io/);
* [@KreyuDataTable/themes/base.html.twig](https://github.com/Kreyu/data-table-bundle/blob/main/src/Resources/views/themes/base.html.twig) - base HTML template;

By default, the [@KreyuDataTable/themes/base.html.twig](https://github.com/Kreyu/data-table-bundle/blob/main/src/Resources/views/themes/base.html.twig) theme is used.

{% hint style="info" %}
**Note:** the default template provides minimal HTML required to properly display the data table. It does **not** support filtering, exporting and personalization features!
{% endhint %}

## Selecting a theme <a href="#selecting-a-theme" id="selecting-a-theme"></a>

To select a theme, provide which one to use in the bundle configuration file.

For example, in order to use the [Bootstrap 5](https://getbootstrap.com/docs/5.0/) theme:

{% tabs %}
{% tab title="YAML" %}
{% code title="config/packages/kreyu_data_table.yaml" lineNumbers="true" %}
```yaml
kreyu_data_table:
  themes:
    - '@KreyuDataTable/themes/bootstrap_5.html.twig'
```
{% endcode %}
{% endtab %}

{% tab title="PHP" %}
{% code title="config/packages/kreyu_data_table.php" lineNumbers="true" %}
```php
use Symfony\Config\KreyuDataTableConfig;

return static function (KreyuDataTableConfig $config) {
    $config->themes([
        '@KreyuDataTable/themes/bootstrap_5.html.twig',
    ]);
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

For more information, see [theme configuration reference](configuration.md#themes).

## Customizing existing theme <a href="#customizing-existing-theme" id="customizing-existing-theme"></a>

To customize existing theme, you can either:

* create a template that extends one of the built-in themes;
* create a template that [overrides the built-in theme](https://symfony.com/doc/current/bundles/override.html#templates);
* create a template from scratch;

Because `themes` configuration option accepts an array of themes, you can provide your own theme with only a fraction of Twig blocks, using the built-in themes as a fallback, for example:

{% tabs %}
{% tab title="YAML" %}
{% code title="config/packages/kreyu_data_table.yaml" lineNumbers="true" %}
```yaml
kreyu_data_table:
  themes:
    - 'templates/data_table/theme.html.twig'
    - '@KreyuDataTable/themes/bootstrap_5.html.twig'
```
{% endcode %}
{% endtab %}

{% tab title="PHP" %}
{% code title="config/packages/kreyu_data_table.php" lineNumbers="true" %}
```php
use Symfony\Config\KreyuDataTableConfig;

return static function (KreyuDataTableConfig $config) {
    $config->themes([
        'templates/data_table/theme.html.twig',
        '@KreyuDataTable/themes/bootstrap_5.html.twig'
    ]);
};
```
{% endcode %}
{% endtab %}
{% endtabs %}

The order in which themes are defined is important because each theme overrides all the previous ones. When rendering a block that is not defined in the theme, the bundle falls back to the previous themes until it finds the block.
