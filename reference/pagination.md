# Pagination

This bundle provides pagination feature, which gives users the ability to display data in chunks, saving memory on huge data sources.

## Configuration

By default, the pagination is enabled for every data table type.

Every part of the feature can be configured using the [data table options](https://data-table-bundle.readthedocs.io/en/latest/reference/pagination/#passing-options-to-data-tables):

* `pagination_enabled` - to enable/disable feature completely;
* `pagination_persistence_enabled` - to enable/disable feature persistence;
* `pagination_persistence_adapter` - to change the persistence adapter;
* `pagination_persistence_subject` - to change the persistence subject directly;

{% hint style="info" %}
**Note**

In the built-in themes, the pagination controls are not visible, if the data table contains only one page! By default, each data table page displays 25 records.
{% endhint %}

## Setting default pagination data

...
