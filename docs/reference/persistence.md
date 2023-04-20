# Persistence

This bundle provides persistence feature, which can be used to save the applied sorting, pagination, filtration and personalization data between requests.

## Configuration

...

## Adapters

Adapters are classes that allows writing (to) and reading (from) the persistent data source. By default, there's only one adapter integrating with Symfony Cache contracts.

### Using the built-in cache adapter <a href="#using-built-in-cache-adapter" id="using-built-in-cache-adapter"></a>

The built-in cache adapter accepts two arguments in constructor:

* cache implementing Symfony's `Symfony\Contracts\Cache\CacheInterface`
* prefix string used to differentiate different data sets, e.g. filtration persistence uses `filtration` prefix

In service container, it is registered as an [abstract service](https://symfony.com/doc/current/service\_container/parent\_services.html):

```sh
$ bin/console debug:container kreyu_data_table.persistence.adapter.cache
```

Creating new services based on the abstract adapter can be performed in service container.

#### Creating custom adapters <a href="#creating-custom-adapters" id="creating-custom-adapters"></a>

To create a custom adapter, create a class that implements `PersistenceAdapterInterface`:

{% code title="src/DataTable/Persistence/DatabasePersistenceAdapter.php" lineNumbers="true" %}
```php
use Kreyu\Bundle\DataTableBundle\Persistence\PersistenceAdapterInterface;

class DatabasePersistenceAdapter implements PersistenceAdapterInterface
{
    public function __construct(
        private EntityManagerInterface $entityManager,
        private string $prefix,
    ) {
    }

    public function read(DataTableInterface $dataTable, PersistenceSubjectInterface $subject): mixed
    {
        // ...
    }

    public function write(DataTableInterface $dataTable, PersistenceSubjectInterface $subject, mixed $data): void
    {
        // ...
    }
}
```
{% endcode %}

...and register it in the container as an abstract service:

{% code title="config/services.yaml" overflow="wrap" %}
```yaml
services:
  app.data_table.persistence.adapter.database:
    class: App\DataTable\Persistence\DatabasePersistenceAdapter
    abstract: true
    arguments:
      - '@doctrine.orm.entity_manager'
```
{% endcode %}

The prefix argument is used to differentiate persistence data between features- therefore a separate service declaration is recommended for each context.&#x20;

Now, create a separate service definition, based on above abstract service, for each feature that will use the adapter. For example, let's focus on the personalization feature alone:

{% code title="config/services.yaml" lineNumbers="true" %}
```yaml
services:
    app.data_table.personalization.persistence.adapter.database:
        parent: app.data_table.persistence.adapter.database
        arguments:
            $prefix: personalization
        tags:
            - { name: kreyu_data_table.persistence.adapter }
```
{% endcode %}

Now you can set the database personalization persistence adapter as default:

{% code title="config/packages/kreyu_data_table.yaml" lineNumbers="true" %}
```yaml
kreyu_data_table:
    defaults:
        personalization:
            persistence_adapter: app.data_table.personalization.persistence.adapter.database
```
{% endcode %}

...or configure it per data table using the `personalization_persistence_adapter` option.

Same logic applies to every other feature that supports the persistence- sorting, pagination and filtration.

## Subjects and their providers

Persistence subjects are classes that implement the `PersistenceSubjectInterface`.\
The value returned in the `getDataTablePersistenceIdentifier()` is used in [persistence adapters](https://data-table-bundle.readthedocs.io/en/latest/reference/persistence/#persistence-adapters) to associate persistent data with the subject.

Persistence subject providers are classes that allows retrieving the persistence subjects.\
Those classes contain `provide()` method, that should return the subject, or throw an `PersistenceSubjectNotFoundException`.

By default, there's only one provider- integrating with Symfony token storage, to retrieve currently logged-in user. The token storage persistence subject provider uses the [UserInterface getUserIdentifier() method](https://github.com/symfony/symfony/blob/6.3/src/Symfony/Component/Security/Core/User/UserInterface.php#L60) as the persistence identifier. If you wish to override this behavior without modifying the user's `getUserIdentifier()` method, implement the `PersistenceSubjectInterface` manually on the User entity:

{% code title="src/Entity/User.php" lineNumbers="true" %}
```php
use Kreyu\Bundle\DataTableBundle\Persistence\PersistenceSubjectInterface;

class User implements PersistenceSubjectInterface
{
    private Uuid $uuid;

    public function getDataTablePersistenceIdentifier(): string
    {
        return (string) $this->uuid;
    }
}
```
{% endcode %}
