# Adding columns

The data table builder object can be used to describe the columns used in the table.\
Similar to data tables, the columns are using the [Types API](../philosophy/understanding-the-types-api.md).

## Adding columns to the data table

Let's start by adding a column for each field in the product entity:

{% code title="src/DataTable/Type/ProductDataTableType.php" lineNumbers="true" %}
```php
use Kreyu\Bundle\DataTableBundle\DataTableBuilderInterface;
use Kreyu\Bundle\DataTableBundle\Type\AbstractDataTableType;

class ProductDataTableType extends AbstractDataTableType
{
    public function buildDataTable(DataTableBuilderInterface $builder, array $options): void
    {
        $builder
            ->addColumn('id', NumberColumnType::class)
            ->addColumn('name', TextColumnType::class)
            ->addColumn('createdAt', DateTimeColumnType::class)
        ;
    }
}
```
{% endcode %}

First argument represents a column name, which also represents a property in the Product entity.\
The second argument represents a fully qualified class name of a column type, which similarly to data table type classes, works as a blueprint for a column - and describes how to render it.

For reference, see [built-in column types](../reference/columns/types.md).

## Making the columns sortable

The column types are customizable using the options array. The options can be passed as the third argument of the builder's `addColumn()` method. By default, columns are **not** sortable, because their `sort` option equals `false`. To change that, set the option to `true`:

{% code title="src/DataTable/Type/ProductDataTableType.php" lineNumbers="true" %}
```php
use Kreyu\Bundle\DataTableBundle\DataTableBuilderInterface;
use Kreyu\Bundle\DataTableBundle\Type\AbstractDataTableType;

class ProductDataTableType extends AbstractDataTableType
{
    public function buildDataTable(DataTableBuilderInterface $builder, array $options): void
    {
        $builder
            ->addColumn('id', NumberColumnType::class, [
                'sort' => true,
            ])
            ->addColumn('name', TextColumnType::class)
            ->addColumn('createdAt', DateTimeColumnType::class)
        ;
    }
}
```
{% endcode %}

Now that the data table contains some columns, let's [render it](../usage/rendering-the-table.md) to the user.
