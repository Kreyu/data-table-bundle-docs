# Creating data providers

In order to support various data sources, the bundle uses the "proxy query" classes.\
Proxy queries are classes that implements [ProxyQueryInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Query/ProxyQueryInterface.php).

They work as an "adapter" or "provider" for the data source you're using in the application. For example, if you want to display a list of products from the database, and your application uses Doctrine ORM, then you'd want to use the built-in [DoctrineOrmProxyQuery](https://github.com/Kreyu/data-table-bundle/blob/main/src/Bridge/Doctrine/Orm/Query/DoctrineOrmProxyQuery.php). If your data comes from another source (from an array, from CSV, etc.), then you can create a custom proxy query class.

## Creating custom proxy query <a href="#creating-custom-proxy-query" id="creating-custom-proxy-query"></a>

To create a custom request handler, create a class that implements [ProxyQueryInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Query/ProxyQueryInterface.php):

{% code title="src/DataTable/Query/ArrayProxyQuery.php" lineNumbers="true" %}
```php
namespace App\DataTable\Query;

use Kreyu\Bundle\DataTableBundle\Pagination\PaginationInterface;
use Kreyu\Bundle\DataTableBundle\Query\ProxyQueryInterface;

class ArrayProxyQuery implements ProxyQueryInterface
{
    private int $page = 1;
    private int $perPage = 25;
    private int $offset = 0;

    public function __construct(
        private array $data,
    ) {
    }

    public function sort(SortingData $sortingData): void
    {
        // ...
    }

    public function paginate(PaginationData $paginationData): void
    {
        $this->page = $paginationData->getPage();
        $this->perPage = $paginationData->getPerPage();
        $this->offset = $paginationData->getOffset();
    }

    public function getPagination(): PaginationInterface
    {
        return new Pagination(
            items: new \LimitIterator(
                new \ArrayIterator($this->data), 
                $this->offset, 
                $this->perPage
            ),
            currentPageNumber: $this->page,
            totalItemCount: count($this->data),
            itemNumberPerPage: $this->perPage,
        );
    }
}
```
{% endcode %}

## Introducing the proxy query factory <a href="#creating-the-proxy-query-factory" id="creating-the-proxy-query-factory"></a>

When using the data table factory, you can pass either the custom proxy query class, or just a data you want to operate on. For example, if you pass Doctrine ORM's QueryBuilder class, it will be automatically converted to the [DoctrineOrmProxyQuery](https://github.com/Kreyu/data-table-bundle/blob/main/src/Bridge/Doctrine/Orm/Query/DoctrineOrmProxyQuery.php) object:

{% code title="src/Controller/ProductController.php" lineNumbers="true" %}
```php
namespace App\Controller;

use App\DataTable\Type\ProductDataTableType;
use App\Repository\ProductRepository;
use Kreyu\Bundle\DataTableBundle\Bridge\Doctrine\Orm\Query\DoctrineOrmProxyQuery;
use Kreyu\Bundle\DataTableBundle\DataTableControllerTrait;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class ProductController extends AbstractController
{
    use DataTableControllerTrait;

    public function index(Request $request, ProductRepository $repository): Response
    {
        $query = $repository->createQueryBuilder('product');

        // Option 1: pass desired ProxyQuery manually:
        $dataTable = $this->createDataTable(
            type: ProductDataTableType::class, 
            query: new DoctrineOrmProxyQuery($query),
        );

        // Option 2: pass the query builder directly, and let the bundle do the work:
        $dataTable = $this->createDataTable(
            type: ProductDataTableType::class, 
            query: $query,
        );

        // ...
    }
}
```
{% endcode %}

This is thanks to the **proxy query factories**. In the background, a [ChainProxyQueryFactory](https://github.com/Kreyu/data-table-bundle/blob/main/src/Query/ChainProxyQueryFactory.php) is used, which iterates on every proxy query factory registered in the container, and returns the first successfully created proxy query.

## Creating custom proxy query factory

To create a custom proxy query factory, create a class that implements [ProxyQueryFactoryInterface](https://github.com/Kreyu/data-table-bundle/blob/main/src/Query/ProxyQueryFactoryInterface.php):

{% code title="src/DataTable/Query/ArrayProxyQueryFactory.php" lineNumbers="true" %}
```php
namespace App\DataTable\Query;

use App\DataTable\Query\ArrayProxyQuery;
use Kreyu\Bundle\DataTableBundle\Exception\UnexpectedTypeException;
use Kreyu\Bundle\DataTableBundle\Query\ProxyQueryFactoryInterface;
use Kreyu\Bundle\DataTableBundle\Query\ProxyQueryInterface;

class ArrayProxyQueryFactory implements ProxyQueryFactoryInterface
{
    public function create(mixed $data): ProxyQueryInterface
    {
        if (!is_array($data)) {
            throw new UnexpectedTypeException($data, ArrayProxyQuery::class);
        }

        return new ArrayProxyQuery($data);
    }
}
```
{% endcode %}

Note that if the custom proxy query does not support a specific data class, you **have** to throw an [UnexpectedTypeException](https://github.com/Kreyu/data-table-bundle/blob/main/src/Exception/UnexpectedTypeException.php), so the chain proxy query factory will know to skip that factory and check other ones.

## Registering the proxy query factory as a service <a href="#registering-the-proxy-query-factory-as-a-service" id="registering-the-proxy-query-factory-as-a-service"></a>

Proxy query factories must be registered as services and tagged with the `kreyu_data_table.proxy_query.factory` tag. If you're using the [default services.yaml configuration](https://symfony.com/doc/current/service\_container.html#service-container-services-load-example), this is already done for you, thanks to [autoconfiguration](https://symfony.com/doc/current/service\_container.html#services-autoconfigure).
