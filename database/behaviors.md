# Database: Behaviors

Model behaviors are used to implement common functionality. Unlike [Traits](traits) these can be implemented either directly in a class or by extending the class. You can read more about behaviors [here](../services/behaviors).

## Encryptable

Similar to the [hashable trait](traits#hashable), encrypted attributes are encrypted when set but also decrypted when an attribute is retrieved. To encrypt attributes in your model, implement the `Winter\Storm\Database\Behaviors\Encryptable` behavior and declare a protected `$encryptable` property with an array containing the attributes to encrypt.

```php
class User extends Model
{
    public $implement = [
        'Winter.Storm.Database.Behaviors.Encryptable',
    ];

    /**
     * @var array List of attributes to encrypt.
     */
    protected $encryptable = ['api_key', 'api_secret'];
}
```

You can also dynamically implement this behavior on third party models outside of your control:

```php
/**
 * Extend the Winter.User user model to implement the encryptable behavior.
 */
Winter\User\Models\User::extend(function ($model) {
    // Implement the sortable behavior dynamically
    $model->implement[] = 'Winter.Storm.Database.Behaviors.Encryptable';
    $model->addDynamicProperty('encryptable', ['encrypted_metadata']);
});
```

> **NOTE:** Encrypted attributes will be serialized and unserialized as a part of the encryption / decryption process. Do not make an attribute that is `encryptable` also [`jsonable`](model#supported-properties) at the same time as the `jsonable` process will attempt to decode a value that has already been unserialized by the encryptor.

## Sortable

Sorted models will store a number value in `sort_order` which maintains the sort order of each individual model in a collection. To store a sort order for your models, implement the `Winter\Storm\Database\Behaviors\Sortable` behavior and ensure that your schema has a column defined for it to use (example: `$table->integer('sort_order')->default(0);`).

```php
class User extends Model
{
    public $implement = [
        'Winter.Storm.Database.Behaviors.Sortable',
    ];
}
```

You can also dynamically implement this behavior on third party models outside of your control:

```php
/**
 * Extend the Winter.User user model to implement the sortable behavior.
 */
Winter\User\Models\User::extend(function ($model) {
    // Implement the sortable behavior dynamically
    $model->implement[] = 'Winter.Storm.Database.Behaviors.Sortable';
});
```

You may modify the key name used to identify the sort order by defining the `SORT_ORDER` constant:

```php
const SORT_ORDER = 'my_sort_order_column';
```

Use the `setSortableOrder` method to set the orders on a single record or multiple records.

```php
// Sets the order of the user to 1...
$user->setSortableOrder($user->id, 1);

// Sets the order of records 1, 2, 3 to 3, 2, 1 respectively...
$user->setSortableOrder([1, 2, 3], [3, 2, 1]);
```

> **NOTE:** If implementing this behavior in a model where data (rows) already existed previously, the data set may need to be initialized before this behavior will work correctly. To do so, either manually update each row's `sort_order` column or run a query against the data to copy the record's `id` column to the `sort_order` column (ex. `UPDATE myvendor_myplugin_mymodelrecords SET sort_order = id`).
