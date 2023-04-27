# Creating data tables

To create a data table, either:

* use the trait to gain access to helpful methods;
* inject data table factory and use it directly;

For the sake of simplicity, the documentation uses the trait method:

{% code title="src/Controller/ProductController.php" lineNumbers="true" %}
```php
use Kreyu\Bundle\DataTableBundle\DataTableFactoryAwareTrait;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;

class ProductController extends AbstractController
{
    use DataTableFactoryAwareTrait;
}
```
{% endcode %}

The trait gives access to three helper methods:

| Method                   | Description                                                                                                                                                  |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `createDataTable`        | Creates data table using the type class.                                                                                                                     |
| `createNamedDataTable`   | Creates data table using the type classes, but explicitly sets its name. Used in cases where the single page displays multiple data tables of the same type. |
| `createDataTableBuilder` | Creates a builder to describe the data table manually, without type classes. In most cases it is used for prototyping rather than actual usage.              |

Therefore, to create a data table, we need

## Creating data table type classes <a href="#creating-data-table-classes" id="creating-data-table-classes"></a>

The data table type classes work as a blueprint. A single type can be used to create as many data tables as needed - making them a nice, reusable piece of code.

The data table type classes must implement their interface, but it is recommended to extend them from the abstract class, which already implements the interface and provides some utilities:

{% code title="src/DataTable/Type/ProductDataTableType.php" lineNumbers="true" %}
```php
use Kreyu\Bundle\DataTableBundle\Type\AbstractDataTableType;

class ProductDataTableType extends AbstractDataTableType
{
}
```
{% endcode %}

Now that the data table type class has been created, it can be used in the controller:

<pre class="language-php" data-title="src/Controller/ProductController.php" data-line-numbers><code class="lang-php">use App\DataTable\Type\ProductDataTableType;
use Kreyu\Bundle\DataTableBundle\DataTableControllerTrait;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;

class ProductController extends AbstractController
{
    use DataTableControllerTrait;
    
    public function index()
    {
<strong>        $dataTable = $this->createDataTable(ProductDataTableType::class);
</strong>    }
}
</code></pre>

The data table requires a data source to operate on - in this example, a Doctrine query. It can be passed as the `$query` argument of the `createDataTable` and `createNamedDataTable` methods:

{% code title="src/Controller/ProductController.php" lineNumbers="true" %}
```php
use App\DataTable\Type\ProductDataTableType;
use App\Repository\ProductRepository;
use Kreyu\Bundle\DataTableBundle\DataTableControllerTrait;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;

class ProductController extends AbstractController
{
    use DataTableControllerTrait;
    
    public function index(ProductRepository $repository)
    {
        $dataTable = $this->createDataTable(
            type: ProductDataTableType::class, 
            query: $repository->createQueryBuilder('product')
        );
    }
}
```
{% endcode %}

Running the code will result in an error:

```markup
*LogicException*: The data table has no configured columns. You must provide them using the builder "addColumn()" method.
```

The message is self-explainatory - the data table has no configured columns, yet.&#x20;

Let's continue to the next chapter - [adding columns](../basic-usage/adding-columns.md).
