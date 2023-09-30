# Database: Traits

Model traits are used to implement common functionality.

## Hashable

Hashed attributes are hashed immediately when the attribute is first set on the model. To hash attributes in your model, apply the `Winter\Storm\Database\Traits\Hashable` trait and declare a protected `$hashable` property with an array containing the attributes to hash.

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\Hashable;

    /**
     * @var array List of attributes to hash.
     */
    protected $hashable = ['password'];
}
```

## Purgeable

Purged attributes will not be saved to the database when a model is created or updated. To purge attributes in your model, apply the `Winter\Storm\Database\Traits\Purgeable` trait and declare a protected `$purgeable` property with an array containing the attributes to purge.

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\Purgeable;

    /**
     * @var array List of attributes to purge.
     */
    protected $purgeable = ['password_confirmation'];
}
```

The defined attributes will be purged when the model is saved, before the [model events](model#events) are triggered, including validation. Use the `getOriginalPurgeValue` to find a value that was purged.

```php
return $user->getOriginalPurgeValue('password_confirmation');
```

## Encryptable

Similar to the [hashable trait](#hashable), encrypted attributes are encrypted when set but also decrypted when an attribute is retrieved. To encrypt attributes in your model, apply the `Winter\Storm\Database\Traits\Encryptable` trait and declare a protected `$encryptable` property with an array containing the attributes to encrypt.

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\Encryptable;

    /**
     * @var array List of attributes to encrypt.
     */
    protected $encryptable = ['api_key', 'api_secret'];
}
```

> **NOTE:** Encrypted attributes will be serialized and unserialized as a part of the encryption / decryption process. Do not make an attribute that is `encryptable` also [`jsonable`](model#supported-properties) at the same time as the `jsonable` process will attempt to decode a value that has already been unserialized by the encryptor.

## Sluggable

Slugs are meaningful codes that are commonly used in page URLs. To automatically generate a unique slug for your model, apply the `Winter\Storm\Database\Traits\Sluggable` trait and declare a protected `$slugs` property.

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\Sluggable;

    /**
     * @var array Generate slugs for these attributes.
     */
    protected $slugs = ['slug' => 'name'];
}
```

The `$slugs` property should be an array where the key is the destination column for the slug and the value is the source string used to generate the slug. In the above example, if the `name` column was set to **Cheyenne**, as a result the `slug` column would be set to **cheyenne**, **cheyenne-2**, or **cheyenne-3**, etc before the model is created.

To generate a slug from multiple sources, pass another array as the source value:

```php
protected $slugs = [
    'slug' => ['first_name', 'last_name']
];
```

The source value also supports using dot notation to generate a slug from a relation's attribute:

```php
protected $slugs = [
    'slug' => ['first_name', 'family.last_name']
];
```

Slugs are only generated when a model first created. To override or disable this functionality, simply set the slug attribute manually:

```php
$user = new User;
$user->name = 'Remy';
$user->slug = 'custom-slug';
$user->save(); // Slug will not be generated
```

Use the `slugAttributes` method to regenerate slugs when updating a model:

```php
$user = User::find(1);
$user->slug = null;
$user->slugAttributes();
$user->save();
```

### Sluggable with the SoftDelete trait

By default, soft deleted models are ignored when the slug is generated.
You might want to prevent slug duplication when recovering soft deleted models.

Set the `$allowTrashedSlugs` attribute to `true` in order to take into account soft deleted records when generating new slugs.

```php
protected $allowTrashedSlugs = true;
```

## Revisionable

Winter models can record the history of changes in values by storing revisions. To store revisions for your model, apply the `Winter\Storm\Database\Traits\Revisionable` trait and declare a protected `$revisionable` property with an array containing the attributes to monitor for changes. You also need to define a `$morphMany` [model relation](relations) called `revision_history` that refers to the `System\Models\Revision` class with the name `revisionable` - this is where the revision history data is stored.

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\Revisionable;

    /**
     * @var array Monitor these attributes for changes.
     */
    protected $revisionable = ['name', 'email'];

    /**
     * @var array Relations
     */
    public $morphMany = [
        'revision_history' => ['System\Models\Revision', 'name' => 'revisionable']
    ];
}
```

By default 500 records will be kept, however this can be modified by declaring a `$revisionableLimit` property on the model with a new limit value.

```php
/**
 * @var int Maximum number of revision records to keep.
 */
public $revisionableLimit = 8;
```

The revision history can be accessed like any other relation:

```php
$history = User::find(1)->revision_history;

foreach ($history as $record) {
    echo $record->field . ' updated ';
    echo 'from ' . $record->old_value;
    echo 'to ' . $record->new_value;
}
```

The revision record optionally supports a user relationship using the `user_id` attribute. You may include a `getRevisionableUser` method in your model to keep track of the user that made the modification.

```php
public function getRevisionableUser()
{
    return BackendAuth::getUser()->id;
}
```

## Sortable

Sorted models will store a number value in `sort_order` which maintains the sort order of each individual model in a collection. To store a sort order for your models, apply the `Winter\Storm\Database\Traits\Sortable` trait and ensure that your schema has a column defined for it to use (example: `$table->integer('sort_order')->default(0);`).

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\Sortable;
}
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

> **NOTE:** If adding this trait to a model where data (rows) already existed previously, the data set may need to be initialized before this trait will work correctly. To do so, either manually update each row's `sort_order` column or run a query against the data to copy the record's `id` column to the `sort_order` column (ex. `UPDATE myvendor_myplugin_mymodelrecords SET sort_order = id`).

## Simple Tree

A simple tree model will use the `parent_id` column maintain a parent and child relationship between models. To use the simple tree, apply the `Winter\Storm\Database\Traits\SimpleTree` trait.

```php
class Category extends Model
{
    use \Winter\Storm\Database\Traits\SimpleTree;
}
```

This trait will automatically inject two [model relations](../database/relations) called `parent` and `children`, it is the equivalent of the following definitions:

```php
public $belongsTo = [
    'parent'    => ['User', 'key' => 'parent_id'],
];

public $hasMany = [
    'children'    => ['User', 'key' => 'parent_id'],
];
```

You may modify the key name used to identify the parent by defining the `PARENT_ID` constant:

```php
const PARENT_ID = 'my_parent_column';
```

Collections of models that use this trait will return the type of `Winter\Storm\Database\TreeCollection` which adds the `toNested` method. To build an eager loaded tree structure, return the records with the relations eager loaded.

```php
Category::all()->toNested();
```

### Rendering

In order to render all levels of items and their children, you can use recursive processing

```twig
{% macro renderChildren(item) %}
    {% import _self as SELF %}
    {% if item.children is not empty %}
        <ul>
            {% for child in item.children %}
                <li>{{ child.name }}{{ SELF.renderChildren(child) | raw }}</li>
            {% endfor %}
        </ul>
    {% endif %}
{% endmacro %}

{% import _self as SELF %}
{{ SELF.renderChildren(category) | raw }}
```

## Nested Tree

The [nested set model](https://en.wikipedia.org/wiki/Nested_set_model) is an advanced technique for maintaining hierachies among models using `parent_id`, `nest_left`, `nest_right`, and `nest_depth` columns.

```php
$table->integer('parent_id')->nullable();
$table->integer('nest_left')->nullable();
$table->integer('nest_right')->nullable();
$table->integer('nest_depth')->nullable();
```

To use a nested set model, apply the `Winter\Storm\Database\Traits\NestedTree` trait. All of the features of the `SimpleTree` trait are inherently available in this model.

```php
class Category extends Model
{
    use \Winter\Storm\Database\Traits\NestedTree;
}
```

You can change the column names used by defining the following constants on the model:

```php
const PARENT_ID = 'my_parent_column';
const NEST_LEFT = 'my_left_column';
const NEST_RIGHT = 'my_right_column';
const NEST_DEPTH = 'my_depth_column';
```

### General access methods

- `$model->getRoot();` - Returns the highest parent of a node.
- `$model->getRootList();` - Returns an indented array of key and value columns from root.
- `$model->getParent();` - The direct parent node.
- `$model->getParents();` - Returns all parents up the tree.
- `$model->getParentsAndSelf();` - Returns all parents up the tree and self.
- `$model->getChildren();` - Set of all direct child nodes.
- `$model->getSiblings();` - Return all siblings (parent's children).
- `$model->getSiblingsAndSelf();` - Return all siblings and self.
- `$model->getLeaves();` - Returns all final nodes without children.
- `$model->getDepth();` - Returns the depth of a current node.
- `$model->getChildCount();` - Returns number of all children.

### Query builder methods

- `$query->withoutNode();` - Filters a specific node from the results.
- `$query->withoutSelf();` - Filters current node from the results.
- `$query->withoutRoot();` - Filters root from the results.
- `$query->children();` - Filters as direct children down the tree.
- `$query->allChildren();` - Filters as all children down the tree.
- `$query->parent();` - Filters as direct parent up the tree.
- `$query->parents();` - Filters as all parents up the tree.
- `$query->siblings();` - Filters as all siblings (parent's children).
- `$query->leaves();` - Filters as all final nodes without children.
- `$query->getNested();` - Returns an eager loaded collection of results.
- `$query->listsNested();` - Returns an indented array of key and value columns.

### Flat result access methods

- `$model->getAll();` - Returns everything in correct order.
- `$model->getAllRoot();` - Returns all root nodes.
- `$model->getAllChildren();` - Returns all children down the tree.
- `$model->getAllChildrenAndSelf();` - Returns all children and self.

### Eager loaded access methods

- `$model->getEagerRoot();` - Returns a list of all root nodes, with ->children eager loaded.
- `$model->getEagerChildren();` - Returns direct child nodes, with ->children eager loaded.

### Creating a root node

By default, all nodes are created as roots:

```php
$root = Category::create(['name' => 'Root category']);
```

Alternatively, you may find yourself in the need of converting an existing node into a root node:

```php
$node->makeRoot();
```

You may also nullify it's `parent_id` column which works the same as `makeRoot'.

```php
$node->parent_id = null;
$node->save();
```

### Inserting nodes

You can insert new nodes directly by the relation:

```php
$child1 = $root->children()->create(['name' => 'Child 1']);
```

Or use the `makeChildOf` method for existing nodes:

```php
$child2 = Category::create(['name' => 'Child 2']);
$child2->makeChildOf($root);
```

### Deleting nodes

When a node is deleted with the `delete` method, all descendants of the node will also be deleted. Note that the delete [model events](model#events) will not be fired for the child models.

```php
$child1->delete();
```

### Getting the nesting level of a node

The `getLevel` method will return current nesting level, or depth, of a node.

```php
// 0 when root
$node->getLevel()
```

### Moving nodes around

There are several methods for moving nodes around:

- `moveLeft()`: Find the left sibling and move to the left of it.
- `moveRight()`: Find the right sibling and move to the right of it.
- `moveBefore($otherNode)`: Move to the node to the left of ...
- `moveAfter($otherNode)`: Move to the node to the right of ...
- `makeChildOf($otherNode)`: Make the node a child of ...
- `makeRoot()`: Make current node a root node.

## Validation

Winter models uses the built-in [Validator class](../services/validation). The validation rules are defined in the model class as a public property named `$rules` and the class must use the trait `Winter\Storm\Database\Traits\Validation`:

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\Validation;

    public $rules = [
        'name'                  => 'required|between:4,16',
        'email'                 => 'required|email',
        'password'              => 'required|alpha_num|between:4,8|confirmed',
        'password_confirmation' => 'required|alpha_num|between:4,8'
    ];
}
```

> **NOTE**: You're free to use the [array syntax](../services/validation#validating-arrays) for validation rules as well.

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\Validation;

    public $rules = [
        'links.*.url'    => 'required|url',
        'links.*.anchor' => 'required'
    ];
}
```

Models validate themselves automatically when the `save` method is called.

```php
$user = new User;
$user->name = 'Actual Person';
$user->email = 'a.person@example.com';
$user->password = 'passw0rd';

// Returns false if model is invalid
$success = $user->save();
```

> **NOTE:** You can also validate a model at any time using the `validate` method.

### Retrieving validation errors

When a model fails to validate, a `Illuminate\Support\MessageBag` object is attached to the model. The object which contains validation failure messages. Retrieve the validation errors message collection instance with `errors` method or `$validationErrors` property. Retrieve all validation errors with `errors()->all()`. Retrieve errors for a *specific* attribute using `validationErrors->get('attribute')`.

> **NOTE:** The Model leverages the MessagesBag object which has a [simple and elegant method](../services/validation#working-with-error-messages) of formatting errors.

### Overriding validation

The `forceSave` method validates the model and saves regardless of whether or not there are validation errors.

```php
$user = new User;

// Creates a user without validation
$user->forceSave();
```

### Custom error messages

Just like the Validator class, you can set custom error messages using the [same syntax](../services/validation#custom-error-messages).

```php
class User extends Model
{
    public $customMessages = [
        'required' => 'The :attribute field is required.',
        ...
    ];
}
```

You can also add custom error messages to the array syntax for validation rules as well.

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\Validation;

    public $rules = [
        'links.*.url'    => 'required|url',
        'links.*.anchor' => 'required'
    ];

    public $customMessages = [
        'links.*.url.required'    => 'The url is required',
        'links.*.url.*'           => 'The url needs to be a valid url',
        'links.*.anchor.required' => 'The anchor text is required',
    ];
}
```

In the above example you can write custom error messages to specific validation rules (here we used: `required`). Or you can use a `*` to select everything else (here we added a custom message to the `url` validation rule using the `*`).

### Custom attribute names

You may also set custom attribute names with the `$attributeNames` array.

```php
class User extends Model
{
    public $attributeNames = [
        'email' => 'Email Address',
        ...
    ];
}
```

### Dynamic validation rules

You can apply rules dynamically by overriding the `beforeValidate` [model event](../database/model#events) method. Here we check if the `is_remote` attribute is `false` and then dynamically set the `latitude` and `longitude` attributes to be required fields.

```php
public function beforeValidate()
{
    if (!$this->is_remote) {
        $this->rules['latitude'] = 'required';
        $this->rules['longitude'] = 'required';
    }
}
```

### Custom validation rules

You can also create custom validation rules the [same way](../services/validation#custom-validation-rules) you would for the Validator service.

## Soft deleting

When soft deleting a model, it is not actually removed from your database. Instead, a `deleted_at` timestamp is set on the record. To enable soft deletes for a model, apply the `Winter\Storm\Database\Traits\SoftDelete` trait to the model and add the deleted_at column to your `$dates` property:

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\SoftDelete;

    protected $dates = ['deleted_at'];
}
```

To add a `deleted_at` column to your table, you may use the `softDeletes` method from a migration:

```php
Schema::table('posts', function ($table) {
    $table->softDeletes();
});
```

Now, when you call the `delete` method on the model, the `deleted_at` column will be set to the current timestamp. When querying a model that uses soft deletes, the "deleted" models will not be included in query results.

To determine if a given model instance has been soft deleted, use the `trashed` method:

```php
if ($user->trashed()) {
    //
}
```

### Querying soft deleted models

#### Including soft deleted models

As noted above, soft deleted models will automatically be excluded from query results. However, you may force soft deleted models to appear in a result set using the `withTrashed` method on the query:

```php
$users = User::withTrashed()->where('account_id', 1)->get();
```

The `withTrashed` method may also be used on a [relationship](relations) query:

```php
$flight->history()->withTrashed()->get();
```

#### Retrieving only soft deleted models

The `onlyTrashed` method will retrieve **only** soft deleted models:

```php
$users = User::onlyTrashed()->where('account_id', 1)->get();
```

#### Restoring soft deleted models

Sometimes you may wish to "un-delete" a soft deleted model. To restore a soft deleted model into an active state, use the `restore` method on a model instance:

```php
$user->restore();
```

You may also use the `restore` method in a query to quickly restore multiple models:

```php
// Restore a single model instance...
User::withTrashed()->where('account_id', 1)->restore();

// Restore all related models...
$user->posts()->restore();
```

#### Permanently deleting models

Sometimes you may need to truly remove a model from your database. To permanently remove a soft deleted model from the database, use the `forceDelete` method:

```php
// Force deleting a single model instance...
$user->forceDelete();

// Force deleting all related models...
$user->posts()->forceDelete();
```

### Soft deleting relations

When two related models have soft deletes enabled, you can cascade the delete event by defining the `softDelete` option in the [relation definition](relations#detailed-definitions). In this example, if the user model is soft deleted, the comments belonging to that user will also be soft deleted.

```php
class User extends Model
{
    use \Winter\Storm\Database\Traits\SoftDelete;

    public $hasMany = [
        'comments' => ['Acme\Blog\Models\Comment', 'softDelete' => true]
    ];
}
```

> **NOTE:** If the soft deleting relation is using a pivot table, you can set the `deletedAtColumn` flag on the relation definition to change the column that will hold the soft deletion date in the pivot table, otherwise, it defaults to `deleted_at`.
> **NOTE:** If the related model does not use the soft delete trait, it will be treated the same as the `delete` option and deleted permanently.

Under these same conditions, when the primary model is restored, all the related models that use the `softDelete` option will also be restored.

```php
// Restore the user and comments
$user->restore();
```

### Soft Delete with Sluggable trait

By default, Sluggable trait will ignore soft deleted models when the slug is generated.
In order to make the model restoration less painful [checkout the Sluggable section](#sluggable-with-the-softdelete-trait).

## Nullable

Nullable attributes are set to `NULL` when left empty. To nullify attributes in your model, apply the `Winter\Storm\Database\Traits\Nullable` trait and declare a `$nullable` property with an array containing the attributes to nullify.

```php
class Product extends Model
{
    use \Winter\Storm\Database\Traits\Nullable;

    /**
     * @var array Nullable attributes.
     */
    protected $nullable = ['sku'];
}
```

## Array Source

> **NOTE:** This functionality requires the PDO SQLite extension to be enabled on your server, even if you intend to use a different database type for your other models.

The Array Source trait allows you to create temporary models that do not rely on database tables, but still provide all the functionality and flexibility of a real database-driven model. This can be useful for custom datasets that you may not wish to store in the database, such as API-provided data or indexable data, but still would like the ability to display this data in widgets that require models, or to use the Eloquent query builder to filter the data.

This functionality works by creating a temporary SQLite database either in memory, or optionally, cached to the filesystem. This SQLite database will be populated with the records that you require automatically, and then all subsequent database queries for this model will be run on this database.

To use this feature, you need to apply the `Winter\Storm\Database\Traits\ArraySource` trait. You will also need to do some configuration in order to define either the records that you intend to populate, or the schema that you wish to use for the temporary database.

```php
class ApiData extends Model
{
    use \Winter\Storm\Database\Traits\ArraySource;

    // ...
}
```

### Configuration

#### Defining the default records

To define a static set of records to populate the database with, you can add a public `$records` property to your model that contains an array set. Each array should contain column names as the key, and column values as the value.

```php
class ApiData extends Model
{
    use \Winter\Storm\Database\Traits\ArraySource;

    public $records = [
        [
            'id' => 1,
            'name' => 'Bugs',
            'animal' => 'Bunny',
            'is_loony_tune' => true,
        ],
        [
            'id' => 2,
            'name' => 'Donald',
            'animal' => 'Duck',
            'is_loony_tune' => false,
        ],
    ];
}
```

If you want to populate records dynamically, you can instead override the `getRecords` method with your own functionality, as long as it returns an array in the same format as the one above.

```php
class ApiData extends Model
{
    use \Winter\Storm\Database\Traits\ArraySource;

    public function getRecords(): array
    {
        $pluginManager = \System\Classes\PluginManager::instance();
        $records = [];

        foreach ($pluginManager->getPlugins() as $code => $plugin) {
            $details = $plugin->pluginDetails();

            $records[] = [
                'code' => $code,
                'name' => $details['name'],
                'description' => $details['description'],
            ];
        }

        return $records;
    }
}
```

You may also opt to not provide either of the above, which effectively creates an empty database, however, you must provide the schema for the table in this instance.

#### Defining the schema

By default, when populating the temporary database with records using the methods above, the schema of the table will be automatically generated based on the data provided. This may not always be accurate, so you may define the schema manually if you would like to control the column types in your temporary database, or if you don't provide any initial records to the model.

To do this, define a public `$recordSchema` property on the model as an array, with the column names as the key and the data type (matching the `$casts` property) as the value.

```php
class ApiData extends Model
{
    use \Winter\Storm\Database\Traits\ArraySource;

    public $records = [
        [
            'id' => 1,
            'name' => 'Bugs',
            'animal' => 'Bunny',
            'is_loony_tune' => true,
        ],
        [
            'id' => 2,
            'name' => 'Donald',
            'animal' => 'Duck',
            'is_loony_tune' => false,
        ],
    ];

    public $recordSchema = [
        'id' => 'integer',
        'name' => 'string',
        'animal' => 'string',
        'is_loony_tune' => 'boolean',
    ];
}
```

## HasSortableRelations

Add this trait to your model in order to allow its relations to be sorted/reordered.

```php
class MyModel extends model
{
    use \Winter\Storm\Database\Traits\HasSortableRelations;

    /**
     * @var array Relations that can be sorted/reordered and the column name to use for sorting/reordering.
     */
    public $sortableRelations = ['relation_name' => 'sort_order_column'];
...
}
