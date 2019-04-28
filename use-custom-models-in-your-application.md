---
title: Using Custom Models in Your Application
excerpt: One of the most common customization of Applications built on top of Vanilo is the extension of models.
date: 2019-04-27
tags: [models, customization, concord, migrations]
---

Vanilo uses [Concord](https://artkonekt.github.io/concord/#/models) to manage its models.
It means that every Vanilo model is ready to be customized by the application.

It's also very important that Vanilo will be aware of the customized model and it will be used
in every piece of the code.

As an example if you customize the `OrderItem` model the `Order::items()` relationship will be
automatically using the customized `OrderItem` model instead of the built in one.

## Custom Model Example

- Let's customize the `OrderItem` model in our application, by adding an additional field and a
relationship to it.
- In the example we will add a `Referral` relationship to order items.
- We assume that the `Referral` model + table already exists in the application.

The customization of the order item model consists of 3 steps:

1. Create the custom model class
2. Register the customized model
3. Create a migration for the extra fields

#### Custom Model Class

```php
namespace App;

use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Vanilo\Order\Models\OrderItem as BaseOrderItem;

class OrderItem extends BaseOrderItem
{
    public function referral(): BelongsTo
    {
        return $this->belongsTo(Referral::class);
    }
}
```

#### Registering the Custom Model

To use the customized model in your application register it in the `AppServiceProvider::boot()`
method:

```php
// app/Providers/AppServiceProvider.php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Vanilo\Order\Contracts\OrderItem as OrderItemContract;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        $this->app->concord->registerModel(
            OrderItemContract::class, \App\OrderItem::class
        );
    }
}
```

#### Create a Migration

Just do it as with any usual Laravel migration:

```php
// database/migrations/yyyy_mm_dd_HHiiss_add_referral_to_order_items.php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class AddReferralToOrderItems extends Migration
{
    public function up()
    {
        Schema::table('order_items', function (Blueprint $table) {
            $table->integer('referral_id')->unsigned();
            $table->foreign('referral_id')->references('id')->on('referrals');
        });
    }

    public function down()
    {
        Schema::table('order_items', function (Blueprint $table) {
            $table->dropColumn('referral_id');
        });
    }
}
```

### What Has Been Achived

1.) Whenever you retrieve an order its items are `App\OrderItem` model instances:

```php
$order = Order::find(1);
$firstItem = $order->items->first();
echo get_class($firstItem);
// App\OrderItem
```

2.) You can set the `referral_id`:

```php
$order = Order::create([
    'number' => 'PO123456'
]);

$order->items()->create([
    'product_type' => 'product',
    'product_id'   => 1234,
    'price'        => 100,
    'name'         => 'Something to sell',
    'referral_id'  => 456,
    'quantity'     => 1
]);
```

3.) You can use the `referral` relationship:

```php
foreach ($order->items as $item) {
    echo get_class($item->referral);
}
// App\Referral
```

### Notes

- If you just want to add simple fields to a model, it's not necessary to use a custom model since
  Eloquent will automatically be able to use those fields. In this case a migration is sufficient
- If you don't want to inherit from the base model you can do so. But you have to implement the base
  interface in your model and you have to register it with Concord's `registerModel` method.
