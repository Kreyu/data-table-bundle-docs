# Introduction

This section of documentation should be treated like a course, as it explains step-by-step how to use the bundle - for complete reference, see the "Reference" section.

## Assumptions

The articles assumes that the project uses Doctrine ORM and contains a Product entity:

{% code title="src/Entity/Product.php" lineNumbers="true" %}
```php
class Product
{
    private int $id;
    private string $name;
    private \DateTimeInterface $createdAt;
    
    public function getId(): int {}
    public function getName(): string {}
    public function getCreatedAt(): \DateTimeInterface {}
}
```
{% endcode %}

For the sake of simplicity, the Doctrine mapping is skipped in the code block above.