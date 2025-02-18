---
title: Using the Product Search
excerpt: It is possible to find products by various criteria using the `ProductSearch` class, including name, slug, taxons, channels, price ranges, sorting, and custom attributes.
date: 2025-02-17
tags: [models, product]
---

The `ProductSearch` class provides a powerful and flexible way to retrieve products based on various criteria. 

It supports:

- Filtering by name, slug, taxon, price range, and property values
- Sorting options and pagination
- Custom attribute filtering

This guide walks you through setting up product listing, implementing filtering, and displaying individual product details.

## Product Listing

The product listing page retrieves and displays all available products.

**Controller**:

```php
namespace App\Http\Controllers;

use Vanilo\Foundation\Search\ProductSearch;

class ProductController extends Controller
{
    private ProductSearch $productFinder;

    public function __construct(ProductSearch $productFinder)
    {
        $this->productFinder = $productFinder;
    }
    
    public function index()
    {
        return view('product.index', [
            'products' => $this->productFinder->getResults(),
        ]);
    }
}
```

**Route**:

```php
use App\Http\Controllers\ProductController;

Route::get('/product-list', [ProductController::class, 'index'])->name('product.index');
```

**View**:

```blade
@foreach($products as $product)
    <a href="{{ route('product.show', $product->slug) }}"
        <img
            @if($product->hasImage())
                src="{{ $product->getThumbnailUrl() }}"
            @else
                src="/images/no_image_placeholder.jpg"
            @endif
            alt="{{ $product->name }}"
        >
    
        <h5>{{ $product->name }}</h5>
        <p>{{ format_price($product->price) }}</p>
    </a>
@endforeach
```

## Product Filtering

Allows users to refine product results based on selected attributes like category, properties, etc.

**Controller**:

```php
namespace App\Http\Controllers;

use App\Http\Requests\ProductIndexRequest;
use Vanilo\Properties\Models\PropertyProxy;
use Vanilo\Foundation\Search\ProductSearch;

class ProductController extends Controller
{
    ...
    
    public function index(ProductIndexRequest $request)
    {
        $properties = PropertyProxy::get();
       
        foreach ($request->filters($properties) as $property => $values) {
            $this->productFinder->havingPropertyValuesByName($property, $values);
        }
    
        return view('product.index', [
            'products' => $this->productFinder->getResults(),
            'properties' => $properties,
            'filters' => $request->filters($properties),
        ]);
    }
}
```

**ProductIndexRequest**:

```php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Support\Collection;

class ProductIndexRequest extends FormRequest
{
    public function filters(Collection $properties): array
    {
        $filters = [];

        foreach ($this->query() as $propertySlug => $values) {
            if ($properties->contains(function ($property) use ($propertySlug) {
                return $property->slug === $propertySlug;
            })) {
                $filters[$propertySlug] = is_array($values) ? $values : [$values];
            }
        }

        return $filters;
    }

    public function rules(): array
    {
        return [];
    }
}
```

**View**:

```blade
<form action="{{ route('product.index') }}">
    Filters

    <ul>
        @foreach($properties as $property)
            <li>{{ $property->name }}:</li>
            
            @foreach($property->values() as $propertyValue)
                <li>
                    <label for="filter-{{$propertyValue->id}}">
                        <input type="checkbox"
                               name="{{ $property->slug }}[]"
                               value="{{ $propertyValue->value }}"
                               id="filter-{{$propertyValue->id}}"
                               @if(in_array($propertyValue->value, $filters)) checked="checked" @endif
                        >
                        {{ $propertyValue->title }}
                    </label>
                </li>
            @endforeach
        @endforeach
    </ul>

    <button type="submit">Apply</button>
</form>
```

## Product Filtering by Category

Enables users to filter products within a specific category while applying additional filters.

**Controller**:

```php
namespace App\Http\Controllers;

use Vanilo\Category\Contracts\Taxon;
use App\Http\Requests\ProductIndexRequest;
use Vanilo\Properties\Models\PropertyProxy;
use Vanilo\Foundation\Search\ProductSearch;

class ProductController extends Controller
{
    ...
    
    public function index(ProductIndexRequest $request, string $taxonomyName = null, Taxon $taxon = null)
    {
        $properties = PropertyProxy::get();
        
        if ($taxon) {
            $this->productFinder->withinTaxon($taxon);
        }
        
        foreach ($request->filters($properties) as $property => $values) {
            $this->productFinder->havingPropertyValuesByName($property, $values);
        }
        
        return view('product.index', [
            'products' => $this->productFinder->getResults(),
            'properties' => $properties,
            'filters' => $request->filters($properties),
            'taxon' => $taxon,
        ]);
    }
```

**Route**:

```php
Route::get('/c/{taxonomyName}/{taxon}', [ProductController::class, 'index'])->name('taxon.show');
```

**View**:

```blade
<form action="{{ $taxon ? route('taxon.show', [$taxon->taxonomy->slug, $taxon]) : route('product.index') }}">
    Filters

    <ul>
        @foreach($properties as $property)
            <li>{{ $property->name }}:</li>
            
            @foreach($property->values() as $propertyValue)
                <li>
                    <label for="filter-{{$propertyValue->id}}">
                        <input type="checkbox"
                               name="{{ $property->slug }}[]"
                               value="{{ $propertyValue->value }}"
                               id="filter-{{$propertyValue->id}}"
                               @if(in_array($propertyValue->value, $filters)) checked="checked" @endif
                        >
                        {{ $propertyValue->title }}
                    </label>
                </li>
            @endforeach
        @endforeach
    </ul>

    <button type="submit">Apply</button>
</form>
```

## Product Details

Once a user selects a product, they should be taken to the product details page.

**Controller**:

```php
namespace App\Http\Controllers;

use Vanilo\Foundation\Search\ProductSearch;

class ProductController extends Controller
{
    ...
    
    public function show(string $slug)
    {
        $product = $this->productFinder->findBySlug($slug);

        if (! $product) {
            abort(404);
        }

        return view('product.show', [
           'product' => $product,
        ]);
    }
}
```

**Route**:

```php
use App\Http\Controllers\ProductController;

Route::get('/p/{slug}', [ProductController::class, 'show'])->name('product.show');
```

**View**:

```blade
<x-app-layout>
    <img src="{{ $product->getImageUrl("square") }}">

    <h1>{{ $product->name }}</h1>
    <p>SKU: {{ $product->sku }}</p>
    <p>{{ format_price($product->price) }}</p>
</x-app-layout>
```

**Displaying Product Properties**:

```blade
@foreach($product->propertyValues as $value)
    {{ $value->property->name }}: {{ $value->title }}
@endforeach
```

For more details, check out the Vanilo.io [documentation](https://vanilo.io/docs/4.x/products).
