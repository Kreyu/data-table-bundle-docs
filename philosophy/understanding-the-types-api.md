# Understanding the Types API

Multiple parts of the bundle, such as columns, filters etc. are described using the type classes. The type classes are similar to the [form types](https://symfony.com/doc/current/reference/forms/types.html), with small differences, making them tailored for the usage in the data tables.

Following parts of the bundle are defined using the type classes:

* data tables
* columns
* filters
* actions
* exporters

## Defining the type <a href="#type-definition" id="type-definition"></a>

The type classes work as a blueprint that defines a configuration how its feature should work. They implement their own, feature-specific interface. For easier usage, there's also an abstract classes, which already implements the interface and provides some utilities.

| Context     | Interface                                                                                                                 | Abstract class                                                                                                          |
| ----------- | ------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| Data tables | [DataTableTypeInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Type/DataTableTypeInterface.php)        | [AbstractDataTableType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Type/AbstractDataTableType.php)        |
| Columns     | [ColumnTypeInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Column/Type/ColumnTypeInterface.php)       | [AbstractColumnType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Column/Type/AbstractColumnType.php)       |
| Filters     | [FilterTypeInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Filter/Type/FilterTypeInterface.php)       | [AbstractFilterType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Filter/Type/AbstractFilterType.php)       |
| Actions     | [ActionTypeInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Action/Type/ActionTypeInterface.php)       | [AbstractActionType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Action/Type/AbstractActionType.php)       |
| Exporters   | [ExporterTypeInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Exporter/Type/ExporterTypeInterface.php) | [AbstractExporterType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Exporter/Type/AbstractExporterType.php) |

### Using the inheritance <a href="#type-inheritance" id="type-inheritance"></a>

For example, let's think of a column type that represents a phone number. In theory, it should extend the existing text column type, only adding a phone number oriented formatting. In practice, the type's class **should not** extend the text type class:

{% hint style="danger" %}
This is <mark style="color:red;">invalid</mark> - do **NOT** use PHP class inheritance!

```php
class PhoneColumnType extends TextColumnType
{
}
```
{% endhint %}

Instead, it should return the FQCN of the parent type in the `getParent()` method:

{% hint style="success" %}
This is <mark style="color:green;">valid</mark> - extend abstract type and return parent's class name!

```php
class PhoneColumnType implements AbstractColumnType
{
    public function getParent(): string
    {
        return TextColumnType::class;
    }
}
```
{% endhint %}

The difference is all about the extensions. Considering the example above, while using the PHP inheritance, a text column [type extensions](understanding-the-types-api.md#defining-the-type-extensions) won't be applied to the phone column type.

### Adding configuration options <a href="#type-extension-definition" id="type-extension-definition"></a>

Each type class contains its own set of options, that can be used to configure the type according to a specific need. The options can be defined in the `configureOptions()` method by using the [option resolver](https://symfony.com/doc/current/components/options\_resolver.html).

## Defining the type extensions

The type extensions allow to easily extend existing types. Those classes contain methods similar as their corresponding feature type classes. They implement their own, feature-specific interface. For easier usage, there's also an abstract classes, which already implements the interface and provides some utilities.

| Context     | Interface                                                                                                                                    | Abstract class                                                                                                                            |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Data tables | [DataTableTypeExtensionInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Extension/Type/DataTableTypeInterface.php)        | [AbstractDataTableTypeExtension](https://github.com/Kreyu/data-table-bundle/blob/main/src/Type/AbstractDataTableExtensionType.php)        |
| Columns     | [ColumnTypeExtensionInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Extension/Column/Type/ColumnTypeInterface.php)       | [AbstractColumnTypeExtension](https://github.com/Kreyu/data-table-bundle/blob/main/src/Column/Type/AbstractColumnExtensionType.php)       |
| Filters     | [FilterTypeExtensionInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Extension/Filter/Type/FilterTypeInterface.php)       | [AbstractFilterTypeExtension](https://github.com/Kreyu/data-table-bundle/blob/main/src/Filter/Type/AbstractFilterExtensionType.php)       |
| Actions     | [ActionTypeExtensionInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Extension/Action/Type/ActionTypeInterface.php)       | [AbstractFilterTypeExtension](https://github.com/Kreyu/data-table-bundle/blob/main/src/Filter/Type/AbstractFilterExtensionType.php)       |
| Exporters   | [ExporterTypeExtensionInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Extension/Exporter/Type/ExporterTypeInterface.php) | [AbstractExporterTypeExtension](https://github.com/Kreyu/data-table-bundle/blob/main/src/Exporter/Type/AbstractExporterExtensionType.php) |

### Configuring the extended types <a href="#type-extension-targets" id="type-extension-targets"></a>

Each type extension class **must** define a list of types that it extends, using the `getExtendedTypes()` method.&#x20;

For example, if you wish to create an extension for a built-in text column type, consider following configuration:

{% code lineNumbers="true" %}
```php
use Kreyu\Bundle\DataTableBundle\Column\Extension\AbstractColumnTypeExtension;
use Kreyu\Bundle\DataTableBundle\Column\Type\TextColumnType;

class TextColumnTypeExtension extends AbstractColumnTypeExtension
{
    public static function getExtendedTypes(): iterable
    {
        return [TextColumnType::class];
    }
}
```
{% endcode %}

To apply extension to _every type_ in the system, use the base type of each part of the bundle. For example, in case of the column types:

{% code lineNumbers="true" %}
```php
use Kreyu\Bundle\DataTableBundle\Column\Type\ColumnType;
use Kreyu\Bundle\DataTableBundle\Column\Extension\AbstractColumnTypeExtension;

class ColumnTypeExtension extends AbstractColumnTypeExtension
{
    public static function getExtendedTypes(): iterable
    {
        return [ColumnType::class];
    } 
}
```
{% endcode %}

For reference, a list of each feature base type class:

| Context     | Base type class                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------- |
| Data tables | [DataTableType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Type/DataTableType.php)        |
| Columns     | [ColumnType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Column/Type/ColumnType.php)       |
| Filters     | [FilterType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Filter/Type/FilterType.php)       |
| Actions     | [ActionType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Action/Type/ActionType.php)       |
| Exporters   | [ExporterType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Exporter/Type/ExporterType.php) |

{% hint style="info" %}
#### Using the generators

Returned value is iterable, therefore it supports returning a generator:

```php
use Kreyu\Bundle\DataTableBundle\Column\Extension\AbstractColumnTypeExtension;
use Kreyu\Bundle\DataTableBundle\Column\Type\ColumnType;

class ColumnTypeExtension extends AbstractColumnTypeExtension
{
    public static function getExtendedTypes(): iterable
    {
        yield ColumnType::class;
    } 
}
```
{% endhint %}

## The type resolving process <a href="#type-resolving" id="type-resolving"></a>

Because types support inheritance & extensions, they have to be **resolved** before usage.

Each part of the bundle that supports the **Types API** contains a resolved type class:

| Context     | Resolved type class                                                                                                     |
| ----------- | ----------------------------------------------------------------------------------------------------------------------- |
| Data tables | [ResolvedDataTableType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Type/ResolvedDataTableType.php)        |
| Columns     | [ResolvedColumnType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Column/Type/ResolvedColumnType.php)       |
| Filters     | [ResolvedFilterType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Filter/Type/ResolvedFilterType.php)       |
| Actions     | [ResolvedActionType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Action/Type/ResolvedActionType.php)       |
| Exporters   | [ResolvedExporterType](https://github.com/Kreyu/data-table-bundle/blob/main/src/Exporter/Type/ResolvedExporterType.php) |

Resolved type classes contain same methods as a non-resolved types, and handles both inheritance & extensions. For example, take a look at implementation of the resolved data table type's `buildDataTable()` method:

{% code lineNumbers="true" %}
```php
public function buildDataTable(DataTableBuilderInterface $builder, array $options): void
{
    $this->parent?->buildDataTable($builder, $options);

    $this->innerType->buildDataTable($builder, $options);

    foreach ($this->typeExtensions as $extension) {
        $extension->buildDataTable($builder, $options);
    }
}
```
{% endcode %}

First, the type's parent method is called, followed by the type itself, then comes the extensions.\
Same flow applies to every resolved type in the bundle.

## Accessing the type registry <a href="#type-registry" id="type-registry"></a>

The registry stores all the types and extensions registered in the system. Those classes can be used to retrieve a specific type or extension using their FQCN.

Each part of the bundle that supports the **Types API** contains its own registry:

| Context     | Resolved type class                                                                                        |
| ----------- | ---------------------------------------------------------------------------------------------------------- |
| Data tables | [DataTableRegistry](https://github.com/Kreyu/data-table-bundle/blob/main/src/DataTableRegistry.php)        |
| Columns     | [ColumnRegistry](https://github.com/Kreyu/data-table-bundle/blob/main/src/Column/ColumnRegistry.php)       |
| Filters     | [FilterRegistry](https://github.com/Kreyu/data-table-bundle/blob/main/src/Filter/FilterRegistry.php)       |
| Actions     | [ActionRegistry](https://github.com/Kreyu/data-table-bundle/blob/main/src/Action/ActionRegistry.php)       |
| Exporters   | [ExporterRegistry](https://github.com/Kreyu/data-table-bundle/blob/main/src/Exporter/ExporterRegistry.php) |

By default, the container is passing all the types & extensions to the registry, thanks to the [ Tagged Services](https://symfony.com/doc/current/service\_container/tags.html). For reference, here's a list of each feature's tags:

| Context     | Type tag                         | Type extension tag                         |
| ----------- | -------------------------------- | ------------------------------------------ |
| Data tables | `kreyu_data_table.type`          | `kreyu_data_table.type_extension`          |
| Columns     | `kreyu_data_table.column.type`   | `kreyu_data_table.column.type_extension`   |
| Filters     | `kreyu_data_table.filter.type`   | `kreyu_data_table.filter.type_extension`   |
| Actions     | `kreyu_data_table.action.type`   | `kreyu_data_table.action.type_extension`   |
| Exporters   | `kreyu_data_table.exporter.type` | `kreyu_data_table.exporter.type_extension` |

Note

Tagged services can have priority, therefore you can define the order the extensions will get loaded:

{% code title="config/services.yaml" lineNumbers="true" %}
```yaml
services:
    App\DataTable\Extension\ExtensionA:
        tags:
            - { name: kreyu_data_table.type_extension, priority: 9 }

    App\DataTable\Extension\ExtensionB:
        tags:
            - { name: kreyu_data_table.type_extension, priority: 10 }
```
{% endcode %}

In the example above, the `ExtensionB` will be applied before the `Extension A`.\
Without a priority specified, the extensions would be applied in the order they are registered.

For more details, see [ Tagged Services with Priority](https://symfony.com/doc/current/service\_container/tags.html#tagged-services-with-priority).
