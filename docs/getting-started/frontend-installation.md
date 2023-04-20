# Frontend installation

This bundle provides front-end scripts created using the [Stimulus JavaScript framework](https://stimulus.hotwired.dev/). \
To begin with, make sure your application uses the [Symfony Stimulus Bridge](https://github.com/symfony/stimulus-bridge).

## Adding the dependencies

In your `package.json` file, add a dependency that links locally to the bundle assets:

{% code title="package.json" %}
```json
{
    "devDependencies": {
        "@kreyu/data-table-bundle": "file:vendor/kreyu/data-table-bundle/assets"
    }
}
```
{% endcode %}

## Enabling the controllers

In your `assets/controller.json` file, enable the desired features controllers:

{% code title="assets/controller.json" %}
```json
{
    "controllers": {
        "@kreyu/data-table-bundle": {
            "personalization": {
                "enabled": true
            }
        }
    }
}
```
{% endcode %}

## Installing the dependencies

Using your preferred package manager, install the dependencies and build the assets:

```sh
$ yarn install && yarn build
```
