---
title: Using the Product Search
excerpt: It is possible to find products by various criteria using the `ProductSearch` class, including name, slug, taxons, channels, price ranges, sorting, and custom attributes.
date: 2025-02-17
tags: [models, product]
---

## Searching Products

The `ProductSearch` class provides a way to search for products and master products based on various criteria. The `ProductSearch` class enables filtering by name, slug, taxons, channels, price ranges, and other attributes.

### Finding Products by Name

#### Exact Match:

This will return products and master products that contain "Shiny Glue" in their name.

```php
$finder = new ProductSearch();
$result = $finder->nameContains('Shiny Glue')->getResults();
```

#### Starts With:

Finds products and master products whose names start with "Mature", such as "Matured Cheese" or "Mature People".

```php
$finder = new ProductSearch();
$result = $finder->nameStartsWith('Mature')->getResults();
```

#### Ends With:

Finds products and master products where the name ends with "Transformator", such as "Bobinated Transformator".

```php
$finder = new ProductSearch();
$result = $finder->nameEndsWith('Transformator')->getResults();
```

#### Multiple Matches:

Returns multiple products and master products containing "Mandarin" in their names.

```php
$finder = new ProductSearch();
$products = $finder->nameContains('Mandarin')->getResults();
```

### Finding Products by Slug

#### Exact Slug Match:

Returns the product with the exact matching slug.

```php
$product = ProductSearch::findBySlug('nintendo-todd-20cm-plush');
```

#### Slug with Exception Handling:

Throws a `ModelNotFoundException` if the product is not found.

```php
use Illuminate\Database\Eloquent\ModelNotFoundException;

try {
    $product = ProductSearch::findBySlugOrFail('non-existent-slug');
} catch (ModelNotFoundException $e) {
    // Handle case where product is not found
}
```

### Filtering Products

#### By Taxon:

```php
use Vanilo\Category\Models\Taxon;

$taxon = Taxon::find(1);
$products = (new ProductSearch())->withinTaxon($taxon)->getResults();
```

#### By Channel:

```php
use Vanilo\Channel\Models\Channel;

$channel = Channel::find(1);
$products = (new ProductSearch())->withinChannel($channel)->getResults();
```

#### By Price Range:

```php
$products = (new ProductSearch())->priceBetween(100, 500)->getResults();
```

### Filtering by Property Values

#### By Single Property Value:

Filters products that have a specific property value.

```php
use App\Models\PropertyValue;

$propertyValue = PropertyValue::find(1);
$products = (new ProductSearch())->havingPropertyValue($propertyValue)->getResults();
```

#### By Multiple Property Values:

Filters products that match multiple property values.

```php
$propertyValues = PropertyValue::whereIn('id', [1, 2, 3])->get();
$products = (new ProductSearch())->havingPropertyValues($propertyValues->toArray())->getResults();
```

#### By Property Name and Values:

Filters products by a property name and its corresponding values.

```php
$products = (new ProductSearch())->havingPropertyValuesByName('color', ['red', 'blue'])->getResults();
```

#### Using OR Conditions for Property Values:

Applies an OR condition when filtering by multiple property values.

```php
$products = (new ProductSearch())->orHavingPropertyValues($propertyValues->toArray())->getResults();
```

### Filtering by Custom Attributes

#### Filtering by Color:

```php
$products = (new ProductSearch())->where('color', 'red')->getResults();
```

#### Filtering by Material:

```php
$products = (new ProductSearch())->where('material', 'cotton')->getResults();
```

### Conclusion

The ProductSearch class provides a robust and flexible way to find products based on various criteria. It supports filtering by names, slugs, taxons, price ranges, sorting options, pagination, property values, and custom attributes. Using AND and OR conditions when filtering by property values allows for precise and versatile product discovery.
