# Rendering the table

The data table is created, therefore the next step is to render it to the user.

## Creating data table view

First, pass the data table view to the template. The data table view is somewhat a read-only representation of a table, created by the `createView(`) method:

<pre class="language-php" data-title="src/Controller/ProductController.php" data-line-numbers><code class="lang-php">use App\DataTable\Type\ProductDataTableType;
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
        
        return $this->render('product/index.html.twig', [
<strong>            'data_table' => $dataTable->createView(),
</strong>        ]);
    }
}
</code></pre>

Now, create the missing template, and render the data table:

{% code title="templates/product/index.html.twig" lineNumbers="true" %}
```twig
<div class="card">
    {{ data_table(data_table) }}
</div>
```
{% endcode %}

Voilà! :sparkles: The Twig helper function handles all the work and renders the data table.

## Selecting a theme

Unfortunately, the rendered data table looks _**awful**._ This is because a default theme is being used, which contains only the HTML necessary to base a custom themes on. To fix that, create bundle configuration file and specify a Bootstrap 5 theme:

{% code title="config/packages/kreyu_data_table.yaml" lineNumbers="true" %}
```yaml
kreyu_data_table:
    themes:
        - '@KreyuDataTable/themes/bootstrap_5.html.twig'
```
{% endcode %}

The table is now rendered properly, using a Bootstrap 5 theme.&#x20;

For reference, see built-in themes.

## Binding request to the data table

Now, when trying to sort the data table by the ID column, **nothing happens** - this is because the data table has _no clue_ the sorting occurred! To fix that, return back to the controller, and use the handy `handleRequest()` method:

<pre class="language-php" data-title="src/Controller/ProductController.php" data-line-numbers><code class="lang-php">use App\DataTable\Type\ProductDataTableType;
use App\Repository\ProductRepository;
use Kreyu\Bundle\DataTableBundle\DataTableControllerTrait;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;

class ProductController extends AbstractController
{
    use DataTableControllerTrait;
    
    public function index(Request $request, ProductRepository $repository)
    {
        $dataTable = $this->createDataTable(
            type: ProductDataTableType::class, 
            query: $repository->createQueryBuilder('product')
        );
        
<strong>        $dataTable->handleRequest($request);
</strong>        
        return $this->render('product/index.html.twig', [
            'data_table' => $dataTable->createView(),
        ]);
    }
}
</code></pre>

Now the data table is fully interactive, by having access to the request object.

Speaking of interactivity, let's let the user [filter the table](defining-the-filters.md).
