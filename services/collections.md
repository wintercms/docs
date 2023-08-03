# Collections

## Introduction

The `Winter\Storm\Support\Collection` class provides a fluent, convenient wrapper for working with arrays of data. For example, check out the following code. We'll create a new collection instance from the array, run the `strtoupper` function on each element, and then remove all empty elements:

```php
$collection = new Winter\Storm\Support\Collection(['stewie', 'brian', null]);

$collection = $collection
    ->map(function ($name) {
        return strtoupper($name);
    })
    ->reject(function ($name) {
        return empty($name);
    })
;
```

The `Collection` class allows you to chain its methods to perform fluent mapping and reducing of the underlying array. In general every `Collection` method returns an entirely new `Collection` instance.

## Creating collections

As described above, passing an array to the constructor of the `Winter\Storm\Support\Collection` class will return a new instance for the given array. So, creating a collection is as simple as:

```php
$collection = new Winter\Storm\Support\Collection([1, 2, 3]);
```

By default, collections of [database models](../database/model) are always returned as `Collection` instances; however, feel free to use the `Collection` class wherever it is convenient for your application.

## Extending Collections

Collections are "macroable", which allows you to add additional methods to the `Collection` class at run time. The `Winter\Storm\Support\Collection` class' `macro` method accepts a closure that will be executed when your macro is called. The macro closure may access the collection's other methods via `$this`, just as if it were a real method of the collection class. For example, the following code adds a `toUpper` method to the `Collection` class:

```php
use Winter\Storm\Support\Collection;
use Winter\Storm\Support\Str;

Collection::macro('toUpper', function () {
    return $this->map(function ($value) {
        return Str::upper($value);
    });
});

$collection = collect(['first', 'second']);

$upper = $collection->toUpper();

// ['FIRST', 'SECOND']
```

Typically, you should declare collection macros in the `boot` method of a [Plugin registration file](../plugin/registration#supported-methods).

## Available methods

For the remainder of this documentation, we'll discuss each method available on the `Collection` class. Remember, all of these methods may be chained for fluently manipulating the underlying array. Furthermore, almost every method returns a new `Collection` instance, allowing you to preserve the original copy of the collection when necessary.

You may select any method from this table to see an example of its usage:

<div class="columned-list">

- [all](#all)
- [average](#average)
- [avg](#avg)
- [chunk](#chunk)
- [chunkWhile](#chunkwhile)
- [collapse](#collapse)
- [collect](#collect)
- [combine](#combine)
- [concat](#concat)
- [contains](#contains)
- [containsOneItem](#containsoneitem)
- [containsStrict](#containsstrict)
- [count](#count)
- [countBy](#countby)
- [crossJoin](#crossjoin)
- [dd](#dd)
- [diff](#diff)
- [diffAssoc](#diffassoc)
- [diffKeys](#diffkeys)
- [doesntContain](#doesntcontain)
- [dump](#dump)
- [duplicates](#duplicates)
- [duplicatesStrict](#duplicatesstrict)
- [each](#each)
- [eachSpread](#eachspread)
- [every](#every)
- [except](#except)
- [filter](#filter)
- [first](#first)
- [firstOrFail](#firstorfail)
- [firstWhere](#firstwhere)
- [flatMap](#flatmap)
- [flatten](#flatten)
- [flip](#flip)
- [forget](#forget)
- [forPage](#forpage)
- [get](#get)
- [groupBy](#groupby)
- [has](#has)
- [hasAny](#hasany)
- [implode](#implode)
- [intersect](#intersect)
- [intersectByKeys](#intersectbykeys)
- [isEmpty](#isempty)
- [isNotEmpty](#isnotempty)
- [join](#join)
- [keyBy](#keyby)
- [keys](#keys)
- [last](#last)
- [lazy](#lazy)
- [macro](#macro)
- [make](#make)
- [map](#map)
- [mapInto](#mapinto)
- [mapSpread](#mapspread)
- [mapToGroups](#maptogroups)
- [mapWithKeys](#mapwithkeys)
- [max](#max)
- [median](#median)
- [merge](#merge)
- [mergeRecursive](#mergerecursive)
- [min](#min)
- [mode](#mode)
- [nth](#nth)
- [only](#only)
- [pad](#pad)
- [partition](#partition)
- [pipe](#pipe)
- [pipeInto](#pipeinto)
- [pipeThrough](#pipethrough)
- [pluck](#pluck)
- [pop](#pop)
- [prepend](#prepend)
- [pull](#pull)
- [push](#push)
- [put](#put)
- [random](#random)
- [range](#range)
- [reduce](#reduce)
- [reduceSpread](#reducespread)
- [reject](#reject)
- [replace](#replace)
- [replaceRecursive](#replacerecursive)
- [reverse](#reverse)
- [search](#search)
- [shift](#shift)
- [shuffle](#shuffle)
- [skip](#skip)
- [skipUntil](#skipuntil)
- [skipWhile](#skipwhile)
- [slice](#slice)
- [sliding](#sliding)
- [sole](#sole)
- [some](#some)
- [sort](#sort)
- [sortBy](#sortby)
- [sortByDesc](#sortbydesc)
- [sortDesc](#sortdesc)
- [sortKeys](#sortkeys)
- [sortKeysDesc](#sortkeysdesc)
- [sortKeysUsing](#sortkeysusing)
- [splice](#splice)
- [split](#split)
- [splitIn](#splitin)
- [sum](#sum)
- [take](#take)
- [takeUntil](#takeuntil)
- [takeWhile](#takewhile)
- [tap](#tap)
- [times](#times)
- [toArray](#toarray)
- [toJson](#tojson)
- [transform](#transform)
- [undot](#undot)
- [union](#union)
- [unique](#unique)
- [uniqueStrict](#uniquestrict)
- [unless](#unless)
- [unlessEmpty](#unlessempty)
- [unlessNotEmpty](#unlessnotempty)
- [unwrap](#unwrap)
- [value](#value)
- [values](#values)
- [when](#when)
- [whenEmpty](#whenempty)
- [whenNotEmpty](#whennotempty)
- [where](#where)
- [whereStrict](#wherestrict)
- [whereBetween](#wherebetween)
- [whereIn](#wherein)
- [whereInStrict](#whereinstrict)
- [whereInstanceOf](#whereinstanceof)
- [whereNotBetween](#wherenotbetween)
- [whereNotIn](#wherenotin)
- [whereNotInStrict](#wherenotinstrict)
- [whereNotNull](#wherenotnull)
- [whereNull](#wherenull)
- [wrap](#wrap)
- [zip](#zip)

</div>

### Method Listing

#### `all()`

The `all` method simply returns the underlying array represented by the collection:

```php
$collection = new Collection([1, 2, 3]);

$collection->all();

// [1, 2, 3]
```

#### `average()`

Alias for the [`avg`](#avg) method.

#### `avg()`

The `avg` method returns the [average value](https://en.wikipedia.org/wiki/Average) of a given key:

```php
$average = collect([['foo' => 10], ['foo' => 10], ['foo' => 20], ['foo' => 40]])->avg('foo');

// 20

$average = collect([1, 1, 2, 4])->avg();

// 2
```

#### `chunk()`

The `chunk` method breaks the collection into multiple, smaller collections of a given size:

```php
$collection = new Collection([1, 2, 3, 4, 5, 6, 7]);

$chunks = $collection->chunk(4);

$chunks->toArray();

// [[1, 2, 3, 4], [5, 6, 7]]
```

This method is especially useful in [CMS pages](../cms/pages) when working with a grid system, such as [Bootstrap](http://getbootstrap.com/css/#grid). Imagine you have a collection of models you want to display in a grid:

```twig
{% for chunk in products.chunk(3) %}
    <div class="row">
        {% for product in chunk %}
            <div class="col-xs-4">{{ product.name }}</div>
        {% endfor %}
    </div>
{% endfor %}
```

#### `chunkWhile()`

The `chunkWhile` method breaks the collection into multiple, smaller collections based on the evaluation of the given callback. The `$chunk` variable passed to the closure may be used to inspect the previous element:

```php
$collection = collect(str_split('AABBCCCD'));

$chunks = $collection->chunkWhile(function ($value, $key, $chunk) {
    return $value === $chunk->last();
});

$chunks->all();

// [['A', 'A'], ['B', 'B'], ['C', 'C', 'C'], ['D']]
```

#### `collapse()`

The `collapse` method collapses a collection of arrays into a flat collection:

```php
$collection = new Collection([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

$collapsed = $collection->collapse();

$collapsed->all();

// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

#### `combine()`

The `combine` method combines the values of the collection, as keys, with the values of another array or collection:

```php
$collection = collect(['name', 'age']);

$combined = $collection->combine(['George', 29]);

$combined->all();

// ['name' => 'George', 'age' => 29]
```

#### `collect()`

The `collect` method returns a new `Collection` instance with the items currently in the collection:

```php
$collectionA = collect([1, 2, 3]);

$collectionB = $collectionA->collect();

$collectionB->all();

// [1, 2, 3]
```

The `collect` method is primarily useful for converting [lazy collections](#lazy-collections) into standard `Collection` instances:

```php
$lazyCollection = LazyCollection::make(function () {
    yield 1;
    yield 2;
    yield 3;
});

$collection = $lazyCollection->collect();

get_class($collection);

// 'Illuminate\Support\Collection'

$collection->all();

// [1, 2, 3]
```

> **Tip:** The `collect` method is especially useful when you have an instance of `Enumerable` and need a non-lazy collection instance. Since `collect()` is part of the `Enumerable` contract, you can safely use it to get a `Collection` instance.

#### `concat()`

The `concat` method appends the given `array` or collection values onto the end of the collection:

```php
$collection = collect(['John Doe']);

$concatenated = $collection->concat(['Jane Doe'])->concat(['name' => 'Johnny Doe']);

$concatenated->all();

// ['John Doe', 'Jane Doe', 'Johnny Doe']
```

#### `contains()`

The `contains` method determines whether the collection contains a given item:

```php
$collection = collect(['name' => 'Desk', 'price' => 100]);

$collection->contains('Desk');

// true

$collection->contains('New York');

// false
```

You may also pass a key / value pair to the `contains` method, which will determine if the given pair exists in the collection:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
]);

$collection->contains('product', 'Bookcase');

// false
```

Finally, you may also pass a callback to the `contains` method to perform your own truth test:

```php
$collection = collect([1, 2, 3, 4, 5]);

$collection->contains(function ($value, $key) {
    return $value > 5;
});

// false
```

The `contains` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`containsStrict`](#containsstrict) method to filter using "strict" comparisons.

#### `containsOneItem()`

The `containsOneItem` method determines whether the collection contains a single item:

```php
collect([])->containsOneItem();

// false

collect(['1'])->containsOneItem();

// true

collect(['1', '2'])->containsOneItem();

// false
```

#### `containsStrict()`

This method has the same signature as the [`contains`](#contains) method; however, all values are compared using "strict" comparisons.

#### `count()`

The `count` method returns the total number of items in the collection:

```php
$collection = new Collection([1, 2, 3, 4]);

$collection->count();

// 4
```

#### `countBy()`

The `countBy` method counts the occurrences of values in the collection. By default, the method counts the occurrences of every element:

```php
$collection = collect([1, 2, 2, 2, 3]);

$counted = $collection->countBy();

$counted->all();

// [1 => 1, 2 => 3, 3 => 1]
```

However, you pass a callback to the `countBy` method to count all items by a custom value:

```php
$collection = collect(['alice@gmail.com', 'bob@yahoo.com', 'carlos@gmail.com']);

$counted = $collection->countBy(function ($email) {
    return substr(strrchr($email, "@"), 1);
});

$counted->all();

// ['gmail.com' => 2, 'yahoo.com' => 1]
```

#### `crossJoin()`

The `crossJoin` method cross joins the collection's values among the given arrays or collections, returning a Cartesian product with all possible permutations:

```php
$collection = collect([1, 2]);

$matrix = $collection->crossJoin(['a', 'b']);

$matrix->all();

/*
    [
        [1, 'a'],
        [1, 'b'],
        [2, 'a'],
        [2, 'b'],
    ]
*/

$collection = collect([1, 2]);

$matrix = $collection->crossJoin(['a', 'b'], ['I', 'II']);

$matrix->all();

/*
    [
        [1, 'a', 'I'],
        [1, 'a', 'II'],
        [1, 'b', 'I'],
        [1, 'b', 'II'],
        [2, 'a', 'I'],
        [2, 'a', 'II'],
        [2, 'b', 'I'],
        [2, 'b', 'II'],
    ]
*/
```

#### `dd()`

The `dd` method dumps the collection's items and ends execution of the script:

```php
$collection = collect(['John Doe', 'Jane Doe']);

$collection->dd();

/*
    Collection {
        #items: array:2 [
            0 => "John Doe"
            1 => "Jane Doe"
        ]
    }
*/
```

If you do not want to stop executing the script, use the [`dump`](#dump) method instead.

#### `diff()`

The `diff` method compares the collection against another collection or a plain PHP `array`:

```php
$collection = new Collection([1, 2, 3, 4, 5]);

$diff = $collection->diff([2, 4, 6, 8]);

$diff->all();

// [1, 3, 5]
```

#### `diffAssoc()`

The `diffAssoc` method compares the collection against another collection or a plain PHP `array` based on its keys and values. This method will return the key / value pairs in the original collection that are not present in the given collection:

```php
$collection = collect([
    'color' => 'orange',
    'type' => 'fruit',
    'remain' => 6
]);

$diff = $collection->diffAssoc([
    'color' => 'yellow',
    'type' => 'fruit',
    'remain' => 3,
    'used' => 6,
]);

$diff->all();

// ['color' => 'orange', 'remain' => 6]
```

#### `diffKeys()`

The `diffKeys` method compares the collection against another collection or a plain PHP `array` based on its keys. This method will return the key / value pairs in the original collection that are not present in the given collection:

```php
$collection = collect([
    'one' => 10,
    'two' => 20,
    'three' => 30,
    'four' => 40,
    'five' => 50,
]);

$diff = $collection->diffKeys([
    'two' => 2,
    'four' => 4,
    'six' => 6,
    'eight' => 8,
]);

$diff->all();

// ['one' => 10, 'three' => 30, 'five' => 50]
```

#### `doesntContain()`

The `doesntContain` method determines whether the collection does not contain a given item. You may pass a closure to the `doesntContain` method to determine if an element does not exist in the collection matching a given truth test:

```php
$collection = collect([1, 2, 3, 4, 5]);

$collection->doesntContain(function ($value, $key) {
    return $value < 5;
});

// false
```

Alternatively, you may pass a string to the `doesntContain` method to determine whether the collection does not contain a given item value:

```php
$collection = collect(['name' => 'Desk', 'price' => 100]);

$collection->doesntContain('Table');

// true

$collection->doesntContain('Desk');

// false
```

You may also pass a key / value pair to the `doesntContain` method, which will determine if the given pair does not exist in the collection:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
]);

$collection->doesntContain('product', 'Bookcase');

// true
```

The `doesntContain` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value.

#### `dump()`

The `dump` method dumps the collection's items:

```php
$collection = collect(['John Doe', 'Jane Doe']);

$collection->dump();

/*
    Collection {
        #items: array:2 [
            0 => "John Doe"
            1 => "Jane Doe"
        ]
    }
*/
```

If you want to stop executing the script after dumping the collection, use the [`dd`](#dd) method instead.

#### `duplicates()`

The `duplicates` method retrieves and returns duplicate values from the collection:

```php
$collection = collect(['a', 'b', 'a', 'c', 'b']);

$collection->duplicates();

// [2 => 'a', 4 => 'b']
```

If the collection contains arrays or objects, you can pass the key of the attributes that you wish to check for duplicate values:

```php
$employees = collect([
    ['email' => 'abigail@example.com', 'position' => 'Developer'],
    ['email' => 'james@example.com', 'position' => 'Designer'],
    ['email' => 'victoria@example.com', 'position' => 'Developer'],
])

$employees->duplicates('position');

// [2 => 'Developer']
```

#### `duplicatesStrict()`

This method has the same signature as the [`duplicates`](#duplicates) method; however, all values are compared using "strict" comparisons.

#### `each()`

The `each` method iterates over the items in the collection and passes each item to a callback:

```php
$collection->each(function ($item, $key) {
    //
});
```

If you would like to stop iterating through the items, you may return `false` from your callback:

```php
$collection->each(function ($item, $key) {
    if (/* some condition */) {
        return false;
    }
});
```

#### `eachSpread()`

The `eachSpread` method iterates over the collection's items, passing each nested item value into the given callback:

```php
$collection = collect([['John Doe', 35], ['Jane Doe', 33]]);

$collection->eachSpread(function ($name, $age) {
    //
});
```

You may stop iterating through the items by returning `false` from the callback:

```php
$collection->eachSpread(function ($name, $age) {
    return false;
});
```

#### `every()`

The `every` method creates a new collection consisting of every n-th element:

```php
$collection = new Collection(['a', 'b', 'c', 'd', 'e', 'f']);

$collection->every(4);

// ['a', 'e']
```

You may optionally pass offset as the second argument:

```php
$collection->every(4, 1);

// ['b', 'f']
```

#### `except()`

The `except` method returns all items in the collection except for those with the specified keys:

```php
$collection = collect(['product_id' => 1, 'price' => 100, 'discount' => false]);

$filtered = $collection->except(['price', 'discount']);

$filtered->all();

// ['product_id' => 1]
```

For the inverse of `except`, see the [only](#only) method.

> **NOTE**  This method's behavior is modified when using [Model Collections](../database/collection#available-methods).

#### `filter()`

The `filter` method filters the collection by a given callback, keeping only those items that pass a given truth test:

```php
$collection = new Collection([1, 2, 3, 4]);

$filtered = $collection->filter(function ($item) {
    return $item > 2;
});

$filtered->all();

// [3, 4]
```

For the inverse of `filter`, see the [reject](#reject) method.

#### `first()`

The `first` method returns the first element in the collection that passes a given truth test:

```php
new Collection([1, 2, 3, 4])->first(function ($value, $key) {
    return $value > 2;
});

// 3
```

You may also call the `first` method with no arguments to get the first element in the collection. If the collection is empty, `null` is returned:

```php
new Collection([1, 2, 3, 4])->first();

// 1
```

#### `firstOrFail()`

The `firstOrFail` method is identical to the `first` method; however, if no result is found, an `Illuminate\Support\ItemNotFoundException` exception will be thrown:

```php
collect([1, 2, 3, 4])->firstOrFail(function ($value, $key) {
    return $value > 5;
});

// Throws ItemNotFoundException...
```

You may also call the `firstOrFail` method with no arguments to get the first element in the collection. If the collection is empty, an `Illuminate\Support\ItemNotFoundException` exception will be thrown:

```php
collect([])->firstOrFail();

// Throws ItemNotFoundException...
```

#### `firstWhere()`

The `firstWhere` method returns the first element in the collection with the given key / value pair:

```php
$collection = collect([
    ['name' => 'Regena', 'age' => null],
    ['name' => 'Linda', 'age' => 14],
    ['name' => 'Diego', 'age' => 23],
    ['name' => 'Linda', 'age' => 84],
]);

$collection->firstWhere('name', 'Linda');

// ['name' => 'Linda', 'age' => 14]
```

You may also call the `firstWhere` method with an operator:

```php
$collection->firstWhere('age', '>=', 18);

// ['name' => 'Diego', 'age' => 23]
```

Like the [where](#where) method, you may pass one argument to the `firstWhere` method. In this scenario, the `firstWhere` method will return the first item where the given item key's value is "truthy":

```php
$collection->firstWhere('age');

// ['name' => 'Linda', 'age' => 14]
```

#### `flatMap()`

The `flatMap` method iterates through the collection and passes each value to the given callback. The callback is free to modify the item and return it, thus forming a new collection of modified items. Then, the array is flattened by a level:

```php
$collection = collect([
    ['name' => 'Sally'],
    ['school' => 'Arkansas'],
    ['age' => 28]
]);

$flattened = $collection->flatMap(function ($values) {
    return array_map('strtoupper', $values);
});

$flattened->all();

// ['name' => 'SALLY', 'school' => 'ARKANSAS', 'age' => '28'];
```

#### `flatten()`

The `flatten` method flattens a multi-dimensional collection into a single dimension:

```php
$collection = new Collection(['name' => 'peter', 'languages' => ['php', 'javascript']]);

$flattened = $collection->flatten();

$flattened->all();

// ['peter', 'php', 'javascript'];
```

#### `flip()`

The `flip` method swaps the collection's keys with their corresponding values:

```php
$collection = new Collection(['name' => 'peter', 'platform' => 'winter']);

$flipped = $collection->flip();

$flipped->all();

// ['peter' => 'name', 'winter' => 'platform']
```

#### `forget()`

The `forget` method removes an item from the collection by its key:

```php
$collection = new Collection(['name' => 'peter', 'platform' => 'winter']);

$collection->forget('name');

$collection->all();

// ['platform' => 'winter']
```

> **NOTE:** Unlike most other collection methods, `forget` does not return a new modified collection; it modifies the collection it is called on.

#### `forPage()`

The `forPage` method returns a new collection containing the items that would be present on a given page number:

```php
$collection = new Collection([1, 2, 3, 4, 5, 6, 7, 8, 9])->forPage(2, 3);

$collection->all();

// [4, 5, 6]
```

The method requires the page number and the number of items to show per page, respectively.

#### `get()`

The `get` method returns the item at a given key. If the key does not exist, `null` is returned:

```php
$collection = new Collection(['name' => 'peter', 'platform' => 'winter']);

$value = $collection->get('name');

// peter
```

You may optionally pass a default value as the second argument:

```php
$collection = new Collection(['name' => 'peter', 'platform' => 'winter']);

$value = $collection->get('foo', 'default-value');

// default-value
```

You may even pass a callback as the default value. The result of the callback will be returned if the specified key does not exist:

```php
$collection->get('email', function () {
    return 'default-value';
});

// default-value
```

#### `groupBy()`

The `groupBy` method groups the collection's items by a given key:

```php
$collection = new Collection([
    ['account_id' => 'account-x10', 'product' => 'Bookcase'],
    ['account_id' => 'account-x10', 'product' => 'Chair'],
    ['account_id' => 'account-x11', 'product' => 'Desk'],
]);

$grouped = $collection->groupBy('account_id');

$grouped->toArray();

/*
    [
        'account-x10' => [
            ['account_id' => 'account-x10', 'product' => 'Bookcase'],
            ['account_id' => 'account-x10', 'product' => 'Chair'],
        ],
        'account-x11' => [
            ['account_id' => 'account-x11', 'product' => 'Desk'],
        ],
    ]
*/
```

In addition to passing a string `key`, you may also pass a callback. The callback should return the value you wish to key the group by:

```php
$grouped = $collection->groupBy(function ($item, $key) {
    return substr($item['account_id'], -3);
});

$grouped->toArray();

/*
    [
        'x10' => [
            ['account_id' => 'account-x10', 'product' => 'Bookcase'],
            ['account_id' => 'account-x10', 'product' => 'Chair'],
        ],
        'x11' => [
            ['account_id' => 'account-x11', 'product' => 'Desk'],
        ],
    ]
*/
```

#### `has()`

The `has` method determines if a given key exists in the collection:

```php
$collection = new Collection(['account_id' => 1, 'product' => 'Desk']);

$collection->has('email');

// false
```

#### `hasAny()`

The `hasAny` method determines whether any of the given keys exist in the collection:

```php
$collection = collect(['account_id' => 1, 'product' => 'Desk', 'amount' => 5]);

$collection->hasAny(['product', 'price']);

// true

$collection->hasAny(['name', 'price']);

// false
```

#### `implode()`

The `implode` method joins the items in a collection. Its arguments depend on the type of items in the collection.

If the collection contains arrays or objects, you should pass the key of the attributes you wish to join, and the "glue" string you wish to place between the values:

```php
$collection = new Collection([
    ['account_id' => 1, 'product' => 'Chair'],
    ['account_id' => 2, 'product' => 'Desk'],
]);

$collection->implode('product', ', ');

// Chair, Desk
```

If the collection contains simple strings or numeric values, simply pass the "glue" as the only argument to the method:

```php
new Collection([1, 2, 3, 4, 5])->implode('-');

// '1-2-3-4-5'
```

#### `intersect()`

The `intersect` method removes any values that are not present in the given `array` or collection:

```php
$collection = new Collection(['Desk', 'Sofa', 'Chair']);

$intersect = $collection->intersect(['Desk', 'Chair', 'Bookcase']);

$intersect->all();

// [0 => 'Desk', 2 => 'Chair']
```

As you can see, the resulting collection will preserve the original collection's keys.

#### `intersectByKeys()`

The `intersectByKeys` method removes any keys from the original collection that are not present in the given `array` or collection:

```php
$collection = collect([
    'serial' => 'UX301', 'type' => 'screen', 'year' => 2009
]);

$intersect = $collection->intersectByKeys([
    'reference' => 'UX404', 'type' => 'tab', 'year' => 2011
]);

$intersect->all();

// ['type' => 'screen', 'year' => 2009]
```

#### `isEmpty()`

The `isEmpty` method returns `true` if the collection is empty; otherwise `false` is returned:

```php
new Collection([])->isEmpty();

// true
```

#### `isNotEmpty()`

The `isNotEmpty` method returns `true` if the collection is not empty; otherwise, `false` is returned:

```php
collect([])->isNotEmpty();

// false
```

#### `join()`

The `join` method joins the collection's values with a string:

```php
collect(['a', 'b', 'c'])->join(', '); // 'a, b, c'
collect(['a', 'b', 'c'])->join(', ', ', and '); // 'a, b, and c'
collect(['a', 'b'])->join(', ', ' and '); // 'a and b'
collect(['a'])->join(', ', ' and '); // 'a'
collect([])->join(', ', ' and '); // ''
```

#### `keyBy()`

Keys the collection by the given key:

```php
$collection = new Collection([
    ['product_id' => 'prod-100', 'name' => 'chair'],
    ['product_id' => 'prod-200', 'name' => 'desk'],
]);

$keyed = $collection->keyBy('product_id');

$keyed->all();

/*
    [
        'prod-100' => ['product_id' => 'prod-100', 'name' => 'Chair'],
        'prod-200' => ['product_id' => 'prod-200', 'name' => 'Desk'],
    ]
*/
```

If multiple items have the same key, only the last one will appear in the new collection.

You may also pass your own callback, which should return the value to key the collection by:

```php
$keyed = $collection->keyBy(function ($item) {
    return strtoupper($item['product_id']);
});

$keyed->all();

/*
    [
        'PROD-100' => ['product_id' => 'prod-100', 'name' => 'Chair'],
        'PROD-200' => ['product_id' => 'prod-200', 'name' => 'Desk'],
    ]
*/
```

#### `keys()`

The `keys` method returns all of the collection's keys:

```php
$collection = new Collection([
    'prod-100' => ['product_id' => 'prod-100', 'name' => 'Chair'],
    'prod-200' => ['product_id' => 'prod-200', 'name' => 'Desk'],
]);

$keys = $collection->keys();

$keys->all();

// ['prod-100', 'prod-200']
```

#### `last()`

The `last` method returns the last element in the collection that passes a given truth test:

```php
new Collection([1, 2, 3, 4])->last(function ($key, $value) {
    return $value < 3;
});

// 2
```

You may also call the `last` method with no arguments to get the last element in the collection. If the collection is empty then `null` is returned.

```php
new Collection([1, 2, 3, 4])->last();

// 4
```

#### `lazy()`

The `lazy` method returns a new [`LazyCollection`](#lazy-collections) instance from the underlying array of items:

```php
$lazyCollection = collect([1, 2, 3, 4])->lazy();

get_class($lazyCollection);

// Illuminate\Support\LazyCollection

$lazyCollection->all();

// [1, 2, 3, 4]
```

This is especially useful when you need to perform transformations on a huge `Collection` that contains many items:

```php
$count = $hugeCollection
    ->lazy()
    ->where('country', 'FR')
    ->where('balance', '>', '100')
    ->count();
```

By converting the collection to a `LazyCollection`, we avoid having to allocate a ton of additional memory. Though the original collection still keeps _its_ values in memory, the subsequent filters will not. Therefore, virtually no additional memory will be allocated when filtering the collection's results.

#### `macro()`

The static `macro` method allows you to add methods to the `Collection` class at run time. Refer to the documentation on [extending collections](#extending-collections) for more information.

#### `make()`

The static `make` method creates a new collection instance. See the [Creating Collections](#creating-collections) section.

#### `map()`

The `map` method iterates through the collection and passes each value to the given callback. The callback is free to modify the item and return it, thus forming a new collection of modified items:

```php
$collection = new Collection([1, 2, 3, 4, 5]);

$multiplied = $collection->map(function ($item, $key) {
    return $item * 2;
});

$multiplied->all();

// [2, 4, 6, 8, 10]
```

> **NOTE:** Like most other collection methods, `map` returns a new collection instance; it does not modify the collection it is called on. If you want to transform the original collection, use the [`transform`](#transform) method.

#### `mapInto()`

The `mapInto()` method iterates over the collection, creating a new instance of the given class by passing the value into the constructor:

```php
class Currency
{
    /**
     * Create a new currency instance.
     *
     * @param  string  $code
     * @return void
     */
    function __construct(string $code)
    {
        $this->code = $code;
    }
}

$collection = collect(['USD', 'EUR', 'GBP']);

$currencies = $collection->mapInto(Currency::class);

$currencies->all();

// [Currency('USD'), Currency('EUR'), Currency('GBP')]
```

#### `mapSpread()`

The `mapSpread` method iterates over the collection's items, passing each nested item value into the given callback. The callback is free to modify the item and return it, thus forming a new collection of modified items:

```php
$collection = collect([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]);

$chunks = $collection->chunk(2);

$sequence = $chunks->mapSpread(function ($even, $odd) {
    return $even + $odd;
});

$sequence->all();

// [1, 5, 9, 13, 17]
```

#### `mapToGroups()`

The `mapToGroups` method groups the collection's items by the given callback. The callback should return an associative array containing a single key / value pair, thus forming a new collection of grouped values:

```php
$collection = collect([
    [
        'name' => 'John Doe',
        'department' => 'Sales',
    ],
    [
        'name' => 'Jane Doe',
        'department' => 'Sales',
    ],
    [
        'name' => 'Johnny Doe',
        'department' => 'Marketing',
    ]
]);

$grouped = $collection->mapToGroups(function ($item, $key) {
    return [$item['department'] => $item['name']];
});

$grouped->toArray();

/*
    [
        'Sales' => ['John Doe', 'Jane Doe'],
        'Marketing' => ['Johnny Doe'],
    ]
*/

$grouped->get('Sales')->all();

// ['John Doe', 'Jane Doe']
```

#### `mapWithKeys()`

The `mapWithKeys` method iterates through the collection and passes each value to the given callback. The callback should return an associative array containing a single key / value pair:

```php
$collection = collect([
    [
        'name' => 'John',
        'department' => 'Sales',
        'email' => 'john@example.com'
    ],
    [
        'name' => 'Jane',
        'department' => 'Marketing',
        'email' => 'jane@example.com'
    ]
]);

$keyed = $collection->mapWithKeys(function ($item) {
    return [$item['email'] => $item['name']];
});

$keyed->all();

/*
    [
        'john@example.com' => 'John',
        'jane@example.com' => 'Jane',
    ]
*/
```

#### `max()`

The `max` method returns the maximum value of a given key:

```php
$max = collect([['foo' => 10], ['foo' => 20]])->max('foo');

// 20

$max = collect([1, 2, 3, 4, 5])->max();

// 5
```

#### `median()`

The `median` method returns the [median value](https://en.wikipedia.org/wiki/Median) of a given key:

```php
$median = collect([['foo' => 10], ['foo' => 10], ['foo' => 20], ['foo' => 40]])->median('foo');

// 15

$median = collect([1, 1, 2, 4])->median();

// 1.5
```

#### `merge()`

The `merge` method merges the given array or collection with the original collection. If a string key in the given items matches a string key in the original collection, the given items's value will overwrite the value in the original collection:

```php
$collection = collect(['product_id' => 1, 'price' => 100]);

$merged = $collection->merge(['price' => 200, 'discount' => false]);

$merged->all();

// ['product_id' => 1, 'price' => 200, 'discount' => false]
```

If the given items's keys are numeric, the values will be appended to the end of the collection:

```php
$collection = collect(['Desk', 'Chair']);

$merged = $collection->merge(['Bookcase', 'Door']);

$merged->all();

// ['Desk', 'Chair', 'Bookcase', 'Door']
```

#### `mergeRecursive()`

The `mergeRecursive` method merges the given array or collection recursively with the original collection. If a string key in the given items matches a string key in the original collection, then the values for these keys are merged together into an array, and this is done recursively:

```php
$collection = collect(['product_id' => 1, 'price' => 100]);

$merged = $collection->mergeRecursive(['product_id' => 2, 'price' => 200, 'discount' => false]);

$merged->all();

// ['product_id' => [1, 2], 'price' => [100, 200], 'discount' => false]
```

#### `min()`

The `min` method returns the minimum value of a given key:

```php
$min = collect([['foo' => 10], ['foo' => 20]])->min('foo');

// 10

$min = collect([1, 2, 3, 4, 5])->min();

// 1
```

#### `mode()`

The `mode` method returns the [mode value](https://en.wikipedia.org/wiki/Mode_(statistics)) of a given key:

```php
$mode = collect([['foo' => 10], ['foo' => 10], ['foo' => 20], ['foo' => 40]])->mode('foo');

// [10]

$mode = collect([1, 1, 2, 4])->mode();

// [1]
```

#### `nth()`

The `nth` method creates a new collection consisting of every n-th element:

```php
$collection = collect(['a', 'b', 'c', 'd', 'e', 'f']);

$collection->nth(4);

// ['a', 'e']
```

You may optionally pass an offset as the second argument:

```php
$collection->nth(4, 1);

// ['b', 'f']
```

#### `only()`

The `only` method returns the items in the collection with the specified keys:

```php
$collection = collect(['product_id' => 1, 'name' => 'Desk', 'price' => 100, 'discount' => false]);

$filtered = $collection->only(['product_id', 'name']);

$filtered->all();

// ['product_id' => 1, 'name' => 'Desk']
```

For the inverse of `only`, see the [except](#except) method.

#### `pad()`

The `pad` method will fill the array with the given value until the array reaches the specified size. This method behaves like the [array_pad](https://secure.php.net/manual/en/function.array-pad.php) PHP function.

To pad to the left, you should specify a negative size. No padding will take place if the absolute value of the given size is less than or equal to the length of the array:

```php
$collection = collect(['A', 'B', 'C']);

$filtered = $collection->pad(5, 0);

$filtered->all();

// ['A', 'B', 'C', 0, 0]

$filtered = $collection->pad(-5, 0);

$filtered->all();

// [0, 0, 'A', 'B', 'C']
```

#### `partition()`

The `partition` method may be combined with the `list` PHP function to separate elements that pass a given truth test from those that do not:

```php
$collection = collect([1, 2, 3, 4, 5, 6]);

list($underThree, $equalOrAboveThree) = $collection->partition(function ($i) {
    return $i < 3;
});

$underThree->all();

// [1, 2]

$equalOrAboveThree->all();

// [3, 4, 5, 6]
```

#### `pipe()`

The `pipe` method passes the collection to the given callback and returns the result:

```php
$collection = collect([1, 2, 3]);

$piped = $collection->pipe(function ($collection) {
    return $collection->sum();
});

// 6
```

#### `pipeInto()`

The `pipeInto` method creates a new instance of the given class and passes the collection into the constructor:

```php
class ResourceCollection
{
    /**
        * The Collection instance.
        */
    public $collection;

    /**
        * Create a new ResourceCollection instance.
        *
        * @param  Collection  $collection
        * @return void
        */
    public function __construct(Collection $collection)
    {
        $this->collection = $collection;
    }
}

$collection = collect([1, 2, 3]);

$resource = $collection->pipeInto(ResourceCollection::class);

$resource->collection->all();

// [1, 2, 3]
```

#### `pipeThrough()`

The `pipeThrough` method passes the collection to the given array of closures and returns the result of the executed closures:

```php
$collection = collect([1, 2, 3]);

$result = $collection->pipeThrough([
    function ($collection) {
        return $collection->merge([4, 5]);
    },
    function ($collection) {
        return $collection->sum();
    },
]);

// 15
```

#### `pluck()`

The `pluck` method retrieves all of the collection values for a given key:

```php
$collection = new Collection([
    ['product_id' => 'prod-100', 'name' => 'Chair'],
    ['product_id' => 'prod-200', 'name' => 'Desk'],
]);

$plucked = $collection->pluck('name');

$plucked->all();

// ['Chair', 'Desk']
```

You may also specify how you wish the resulting collection to be keyed:

```php
$plucked = $collection->pluck('name', 'product_id');

$plucked->all();

// ['prod-100' => 'Desk', 'prod-200' => 'Chair']
```

#### `pop()`

The `pop` method removes and returns the last item from the collection:

```php
$collection = new Collection([1, 2, 3, 4, 5]);

$collection->pop();

// 5

$collection->all();

// [1, 2, 3, 4]
```

#### `prepend()`

The `prepend` method adds an item to the beginning of the collection:

```php
$collection = new Collection([1, 2, 3, 4, 5]);

$collection->prepend(0);

$collection->all();

// [0, 1, 2, 3, 4, 5]
```

#### `pull()`

The `pull` method removes and returns an item from the collection by its key:

```php
$collection = new Collection(['product_id' => 'prod-100', 'name' => 'Desk']);

$collection->pull('name');

// 'Desk'

$collection->all();

// ['product_id' => 'prod-100']
```

#### `push()`

The `push` method appends an item to the end of the collection:

```php
$collection = new Collection([1, 2, 3, 4]);

$collection->push(5);

$collection->all();

// [1, 2, 3, 4, 5]
```

#### `put()`

The `put` method sets the given key and value in the collection:

```php
$collection = new Collection(['product_id' => 1, 'name' => 'Desk']);

$collection->put('price', 100);

$collection->all();

// ['product_id' => 1, 'name' => 'Desk', 'price' => 100]
```

#### `random()`

The `random` method returns a random item from the collection:

```php
$collection = new Collection([1, 2, 3, 4, 5]);

$collection->random();

// 4 - (retrieved randomly)
```

You may optionally pass an integer to `random`. If that integer is more than `1`, a collection of items is returned:

```php
$random = $collection->random(3);

$random->all();

// [2, 4, 5] - (retrieved randomly)
```

#### `range()`

The `range` method returns a collection containing integers between the specified range:

```php
$collection = collect()->range(3, 6);

$collection->all();

// [3, 4, 5, 6]
```

#### `reduce()`

The `reduce` method reduces the collection to a single value, passing the result of each iteration into the subsequent iteration:

```php
$collection = new Collection([1, 2, 3]);

$total = $collection->reduce(function ($carry, $item) {
    return $carry + $item;
});

// 6
```

The value for `$carry` on the first iteration is `null`; however, you may specify its initial value by passing a second argument to `reduce`:

```php
$collection->reduce(function ($carry, $item) {
    return $carry + $item;
}, 4);

// 10
```

#### `reduceSpread()`

The `reduceSpread` method reduces the collection to an array of values, passing the results of each iteration into the subsequent iteration. This method is similar to the `reduce` method; however, it can accept multiple initial values:

```php
[$creditsRemaining, $batch] = Image::where('status', 'unprocessed')
    ->get()
    ->reduceSpread(function ($creditsRemaining, $batch, $image) {
        if ($creditsRemaining >= $image->creditsRequired()) {
            $batch->push($image);

            $creditsRemaining -= $image->creditsRequired();
        }

        return [$creditsRemaining, $batch];
    }, $creditsAvailable, collect());
```

#### `reject()`

The `reject` method filters the collection using the given callback. The callback should return `true` for any items it wishes to remove from the resulting collection:

```php
$collection = new Collection([1, 2, 3, 4]);

$filtered = $collection->reject(function ($item) {
    return $item > 2;
});

$filtered->all();

// [1, 2]
```

For the inverse of the `reject` method, see the [`filter`](#filter) method.

#### `replace()`

The `replace` method behaves similarly to `merge`; however, in addition to overwriting matching items with string keys, the `replace` method will also overwrite items in the collection that have matching numeric keys:

```php
$collection = collect(['Taylor', 'Abigail', 'James']);

$replaced = $collection->replace([1 => 'Victoria', 3 => 'Finn']);

$replaced->all();

// ['Taylor', 'Victoria', 'James', 'Finn']
```

#### `replaceRecursive()`

This method works like `replace`, but it will recur into arrays and apply the same replacement process to the inner values:

```php
$collection = collect(['Taylor', 'Abigail', ['James', 'Victoria', 'Finn']]);

$replaced = $collection->replaceRecursive(['Charlie', 2 => [1 => 'King']]);

$replaced->all();

// ['Charlie', 'Abigail', ['James', 'King', 'Finn']]
```

#### `reverse()`

The `reverse` method reverses the order of the collection's items:

```php
$collection = new Collection([1, 2, 3, 4, 5]);

$reversed = $collection->reverse();

$reversed->all();

// [5, 4, 3, 2, 1]
```

#### `search()`

The `search` method searches the collection for the given value and returns its key if found. If the item is not found, `false` is returned.

```php
$collection = new Collection([2, 4, 6, 8]);

$collection->search(4);

// 1
```

The search is done using a "loose" comparison. To use strict comparison, pass `true` as the second argument to the method:

```php
$collection->search('4', true);

// false
```

Alternatively, you may pass in your own callback to search for the first item that passes your truth test:

```php
$collection->search(function ($item, $key) {
    return $item > 5;
});

// 2
```

#### `shift()`

The `shift` method removes and returns the first item from the collection:

```php
$collection = new Collection([1, 2, 3, 4, 5]);

$collection->shift();

// 1

$collection->all();

// [2, 3, 4, 5]
```

#### `shuffle()`

The `shuffle` method randomly shuffles the items in the collection:

```php
$collection = new Collection([1, 2, 3, 4, 5]);

$shuffled = $collection->shuffle();

$shuffled->all();

// [3, 2, 5, 1, 4] (generated randomly)
```

#### `skip()`

The `skip` method returns a new collection, without the first given amount of items:

```php
$collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

$collection = $collection->skip(4);

$collection->all();

// [5, 6, 7, 8, 9, 10]
```

#### `skipUntil()`

The `skipUntil` method skips over items from the collection until the given callback returns `true` and then returns the remaining items in the collection as a new collection instance:

```php
$collection = collect([1, 2, 3, 4]);

$subset = $collection->skipUntil(function ($item) {
    return $item >= 3;
});

$subset->all();

// [3, 4]
```

You may also pass a simple value to the `skipUntil` method to skip all items until the given value is found:

```php
$collection = collect([1, 2, 3, 4]);

$subset = $collection->skipUntil(3);

$subset->all();

// [3, 4]
```

> **WARNING:**  If the given value is not found or the callback never returns `true`, the `skipUntil` method will return an empty collection.

#### `skipWhile()`

The `skipWhile` method skips over items from the collection while the given callback returns `true` and then returns the remaining items in the collection as a new collection:

```php
$collection = collect([1, 2, 3, 4]);

$subset = $collection->skipWhile(function ($item) {
    return $item <= 3;
});

$subset->all();

// [4]
```

#### `slice()`

The `slice` method returns a slice of the collection starting at the given index:

```php
$collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

$slice = $collection->slice(4);

$slice->all();

// [5, 6, 7, 8, 9, 10]
```

If you would like to limit the size of the returned slice, pass the desired size as the second argument to the method:

```php
$slice = $collection->slice(4, 2);

$slice->all();

// [5, 6]
```

The returned slice will preserve keys by default. If you do not wish to preserve the original keys, you can use the [`values`](#values) method to reindex them.

#### `sliding()`

The `sliding` method returns a new collection of chunks representing a "sliding window" view of the items in the collection:

```php
$collection = collect([1, 2, 3, 4, 5]);

$chunks = $collection->sliding(2);

$chunks->toArray();

// [[1, 2], [2, 3], [3, 4], [4, 5]]
```

This is especially useful in conjunction with the [`eachSpread`](#eachspread) method:

```php
$transactions->sliding(2)->eachSpread(function ($previous, $current) {
    $current->total = $previous->total + $current->amount;
});
```

You may optionally pass a second "step" value, which determines the distance between the first item of every chunk:

```php
$collection = collect([1, 2, 3, 4, 5]);

$chunks = $collection->sliding(3, step: 2);

$chunks->toArray();

// [[1, 2, 3], [3, 4, 5]]
```

#### `sole()`

The `sole` method returns the first element in the collection that passes a given truth test, but only if the truth test matches exactly one element:

```php
collect([1, 2, 3, 4])->sole(function ($value, $key) {
    return $value === 2;
});

// 2
```

You may also pass a key / value pair to the `sole` method, which will return the first element in the collection that matches the given pair, but only if it exactly one element matches:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
]);

$collection->sole('product', 'Chair');

// ['product' => 'Chair', 'price' => 100]
```

Alternatively, you may also call the `sole` method with no argument to get the first element in the collection if there is only one element:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
]);

$collection->sole();

// ['product' => 'Desk', 'price' => 200]
```

If there are no elements in the collection that should be returned by the `sole` method, an `\Illuminate\Collections\ItemNotFoundException` exception will be thrown. If there is more than one element that should be returned, an `\Illuminate\Collections\MultipleItemsFoundException` will be thrown.

#### `some()`

Alias for the [`contains`](#contains) method.

#### `sort()`

The `sort` method sorts the collection:

```php
$collection = new Collection([5, 3, 1, 2, 4]);

$sorted = $collection->sort();

$sorted->values()->all();

// [1, 2, 3, 4, 5]
```

The sorted collection keeps the original array keys. In this example we used the [`values`](#values) method to reset the keys to consecutively numbered indexes.

For sorting a collection of nested arrays or objects, see the [`sortBy`](#sortby) and [`sortByDesc`](#sortbydesc) methods.

If your sorting needs are more advanced, you may pass a callback to `sort` with your own algorithm. Refer to the PHP documentation on [`usort`](http://php.net/manual/en/function.usort.php#refsect1-function.usort-parameters), which is what the collection's `sort` method calls under the hood.

#### `sortBy()`

The `sortBy` method sorts the collection by the given key:

```php
$collection = new Collection([
    ['name' => 'Desk', 'price' => 200],
    ['name' => 'Chair', 'price' => 100],
    ['name' => 'Bookcase', 'price' => 150],
]);

$sorted = $collection->sortBy('price');

$sorted->values()->all();

/*
    [
        ['name' => 'Chair', 'price' => 100],
        ['name' => 'Bookcase', 'price' => 150],
        ['name' => 'Desk', 'price' => 200],
    ]
*/
```

The sorted collection keeps the original array keys. In this example we used the [`values`](#values) method to reset the keys to consecutively numbered indexes.

You can also pass your own callback to determine how to sort the collection values:

```php
$collection = new Collection([
    ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
    ['name' => 'Chair', 'colors' => ['Black']],
    ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
]);

$sorted = $collection->sortBy(function ($product, $key) {
    return count($product['colors']);
});

$sorted->values()->all();

/*
    [
        ['name' => 'Chair', 'colors' => ['Black']],
        ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
        ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
    ]
*/
```

#### `sortByDesc()`

This method has the same signature as the [`sortBy`](#sortby) method, but will sort the collection in the opposite order.

#### `sortDesc()`

This method will sort the collection in the opposite order as the [`sort`](#sort) method:

```php
$collection = collect([5, 3, 1, 2, 4]);

$sorted = $collection->sortDesc();

$sorted->values()->all();

// [5, 4, 3, 2, 1]
```

Unlike `sort`, you may not pass a closure to `sortDesc`. Instead, you should use the [`sort`](#sort) method and invert your comparison.

#### `sortKeys()`

The `sortKeys` method sorts the collection by the keys of the underlying associative array:

```php
$collection = collect([
    'id' => 22345,
    'first' => 'John',
    'last' => 'Doe',
]);

$sorted = $collection->sortKeys();

$sorted->all();

/*
    [
        'first' => 'John',
        'id' => 22345,
        'last' => 'Doe',
    ]
*/
```

#### `sortKeysDesc()`

This method has the same signature as the [`sortKeys`](#sortkeys) method, but will sort the collection in the opposite order.

#### `sortKeysUsing()`

The `sortKeysUsing` method sorts the collection by the keys of the underlying associative array using a callback:

```php
$collection = collect([
    'ID' => 22345,
    'first' => 'John',
    'last' => 'Doe',
]);

$sorted = $collection->sortKeysUsing('strnatcasecmp');

$sorted->all();

/*
    [
        'first' => 'John',
        'ID' => 22345,
        'last' => 'Doe',
    ]
*/
```

The callback must be a comparison function that returns an integer less than, equal to, or greater than zero. For more information, refer to the PHP documentation on [`uksort`](https://www.php.net/manual/en/function.uksort.php#refsect1-function.uksort-parameters), which is the PHP function that `sortKeysUsing` method utilizes internally.

#### `splice()`

The `splice` method removes and returns a slice of items starting at the specified index:

```php
$collection = collect([1, 2, 3, 4, 5]);

$chunk = $collection->splice(2);

$chunk->all();

// [3, 4, 5]

$collection->all();

// [1, 2]
```

You may pass a second argument to limit the size of the resulting chunk:

```php
$collection = collect([1, 2, 3, 4, 5]);

$chunk = $collection->splice(2, 1);

$chunk->all();

// [3]

$collection->all();

// [1, 2, 4, 5]
```

In addition, you can pass a third argument containing the new items to replace the items removed from the collection:

```php
$collection = collect([1, 2, 3, 4, 5]);

$chunk = $collection->splice(2, 1, [10, 11]);

$chunk->all();

// [3]

$collection->all();

// [1, 2, 10, 11, 4, 5]
```

#### `split()`

The `split` method breaks a collection into the given number of groups:

```php
$collection = collect([1, 2, 3, 4, 5]);

$groups = $collection->split(3);

$groups->toArray();

// [[1, 2], [3, 4], [5]]
```

#### `splitIn()`

The `splitIn` method breaks a collection into the given number of groups, filling non-terminal groups completely before allocating the remainder to the final group:

```php
$collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

$groups = $collection->splitIn(3);

$groups->all();

// [[1, 2, 3, 4], [5, 6, 7, 8], [9, 10]]
```

#### `sum()`

The `sum` method returns the sum of all items in the collection:

```php
new Collection([1, 2, 3, 4, 5])->sum();

// 15
```

If the collection contains nested arrays or objects, you should pass a key to use for determining which values to sum:

```php
$collection = new Collection([
    ['name' => 'JavaScript: The Good Parts', 'pages' => 176],
    ['name' => 'JavaScript: The Definitive Guide', 'pages' => 1096],
]);

$collection->sum('pages');

// 1272
```

In addition, you may pass your own callback to determine which values of the collection to sum:

```php
$collection = new Collection([
    ['name' => 'Chair', 'colors' => ['Black']],
    ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
    ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
]);

$collection->sum(function ($product) {
    return count($product['colors']);
});

// 6
```

#### `take()`

The `take` method returns a new collection with the specified number of items:

```php
$collection = new Collection([0, 1, 2, 3, 4, 5]);

$chunk = $collection->take(3);

$chunk->all();

// [0, 1, 2]
```

You may also pass a negative integer to take the specified amount of items from the end of the collection:

```php
$collection = new Collection([0, 1, 2, 3, 4, 5]);

$chunk = $collection->take(-2);

$chunk->all();

// [4, 5]
```

#### `takeUntil()`

The `takeUntil` method returns items in the collection until the given callback returns `true`:

```php
$collection = collect([1, 2, 3, 4]);

$subset = $collection->takeUntil(function ($item) {
    return $item >= 3;
});

$subset->all();

// [1, 2]
```

You may also pass a simple value to the `takeUntil` method to get the items until the given value is found:

```php
$collection = collect([1, 2, 3, 4]);

$subset = $collection->takeUntil(3);

$subset->all();

// [1, 2]
```

> **WARNING:** If the given value is not found or the callback never returns `true`, the `takeUntil` method will return all items in the collection.

#### `takeWhile()`

The `takeWhile` method returns items in the collection until the given callback returns `false`:

```php
$collection = collect([1, 2, 3, 4]);

$subset = $collection->takeWhile(function ($item) {
    return $item < 3;
});

$subset->all();

// [1, 2]
```

> **WARNING:** If the callback never returns `false`, the `takeWhile` method will return all items in the collection.

#### `tap()`

The `tap` method passes the collection to the given callback, allowing you to "tap" into the collection at a specific point and do something with the items while not affecting the collection itself:

```php
collect([2, 4, 3, 1, 5])
    ->sort()
    ->tap(function ($collection) {
        Log::debug('Values after sorting', $collection->values()->toArray());
    })
    ->shift();

// 1
```

#### `times()`

The static `times` method creates a new collection by invoking the callback a given amount of times:

```php
$collection = Collection::times(10, function ($number) {
    return $number * 9;
});

$collection->all();

// [9, 18, 27, 36, 45, 54, 63, 72, 81, 90]
```

This method can be useful when combined with factories to create Eloquent models:

```php
$categories = Collection::times(3, function ($number) {
    return factory(Category::class)->create(['name' => "Category No. $number"]);
});

$categories->all();

/*
    [
        ['id' => 1, 'name' => 'Category No. 1'],
        ['id' => 2, 'name' => 'Category No. 2'],
        ['id' => 3, 'name' => 'Category No. 3'],
    ]
*/
```

#### `toArray()`

The `toArray` method converts the collection into a plain PHP `array`. If the collection's values are [database models](../database/model), the models will also be converted to arrays:

```php
$collection = new Collection(['name' => 'Desk', 'price' => 200]);

$collection->toArray();

/*
    [
        ['name' => 'Desk', 'price' => 200],
    ]
*/
```

> **NOTE:** `toArray` also converts all of its nested objects to an array. If you want to get the underlying array as is, use the [`all`](#all) method instead.

#### `toJson()`

The `toJson` method converts the collection into JSON:

```php
$collection = new Collection(['name' => 'Desk', 'price' => 200]);

$collection->toJson();

// '{"name":"Desk","price":200}'
```

#### `transform()`

The `transform` method iterates over the collection and calls the given callback with each item in the collection. The items in the collection will be replaced by the values returned by the callback:

```php
$collection = new Collection([1, 2, 3, 4, 5]);

$collection->transform(function ($item, $key) {
    return $item * 2;
});

$collection->all();

// [2, 4, 6, 8, 10]
```

> **NOTE:** Unlike most other collection methods, `transform` modifies the collection itself. If you wish to create a new collection instead, use the [`map`](#map) method.

#### `undot()`

The `undot` method expands a single-dimensional collection that uses "dot" notation into a multi-dimensional collection:

```php
$person = collect([
    'name.first_name' => 'Marie',
    'name.last_name' => 'Valentine',
    'address.line_1' => '2992 Eagle Drive',
    'address.line_2' => '',
    'address.suburb' => 'Detroit',
    'address.state' => 'MI',
    'address.postcode' => '48219'
]);

$person = $person->undot();

$person->toArray();

/*
    [
        "name" => [
            "first_name" => "Marie",
            "last_name" => "Valentine",
        ],
        "address" => [
            "line_1" => "2992 Eagle Drive",
            "line_2" => "",
            "suburb" => "Detroit",
            "state" => "MI",
            "postcode" => "48219",
        ],
    ]
*/
```

#### `union()`

The `union` method adds the given array to the collection. If the given array contains keys that are already in the original collection, the original collection's values will be preferred:

```php
$collection = collect([1 => ['a'], 2 => ['b']]);

$union = $collection->union([3 => ['c'], 1 => ['b']]);

$union->all();

// [1 => ['a'], 2 => ['b'], 3 => ['c']]
```

#### `unique()`

The `unique` method returns all of the unique items in the collection. The returned collection keeps the original array keys, so in this example we'll use the [`values`](#values) method to reset the keys to consecutively numbered indexes:

```php
$collection = collect([1, 1, 2, 2, 3, 4, 2]);

$unique = $collection->unique();

$unique->values()->all();

// [1, 2, 3, 4]
```

When dealing with nested arrays or objects, you may specify the key used to determine uniqueness:

```php
$collection = collect([
    ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
    ['name' => 'iPhone 5', 'brand' => 'Apple', 'type' => 'phone'],
    ['name' => 'Apple Watch', 'brand' => 'Apple', 'type' => 'watch'],
    ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
    ['name' => 'Galaxy Gear', 'brand' => 'Samsung', 'type' => 'watch'],
]);

$unique = $collection->unique('brand');

$unique->values()->all();

/*
    [
        ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
        ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
    ]
*/
```

You may also pass your own callback to determine item uniqueness:

```php
$unique = $collection->unique(function ($item) {
    return $item['brand'].$item['type'];
});

$unique->values()->all();

/*
    [
        ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
        ['name' => 'Apple Watch', 'brand' => 'Apple', 'type' => 'watch'],
        ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
        ['name' => 'Galaxy Gear', 'brand' => 'Samsung', 'type' => 'watch'],
    ]
*/
```

The `unique` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`uniqueStrict`](#uniquestrict) method to filter using "strict" comparisons.

#### `uniqueStrict()`

This method has the same signature as the [`unique`](#unique) method; however, all values are compared using "strict" comparisons.

#### `unless()`

The `unless` method will execute the given callback unless the first argument given to the method evaluates to `true`:

```php
$collection = collect([1, 2, 3]);

$collection->unless(true, function ($collection) {
    return $collection->push(4);
});

$collection->unless(false, function ($collection) {
    return $collection->push(5);
});

$collection->all();

// [1, 2, 3, 5]
```

For the inverse of `unless`, see the [`when`](#when) method.

#### `unlessEmpty()`

Alias for the [`whenNotEmpty`](#whennotempty) method.

#### `unlessNotEmpty()`

Alias for the [`whenEmpty`](#whenempty) method.

#### `unwrap()`

The static `unwrap` method returns the collection's underlying items from the given value when applicable:

```php
Collection::unwrap(collect('John Doe'));

// ['John Doe']

Collection::unwrap(['John Doe']);

// ['John Doe']

Collection::unwrap('John Doe');

// 'John Doe'
```

#### `value()`

The `value` method retrieves a given value from the first element of the collection:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Speaker', 'price' => 400],
]);

$value = $collection->value('price');

// 200
```

#### `values()`

The `values` method returns a new collection with the keys reset to consecutive integers:

```php
$collection = new Collection([
    10 => ['product' => 'Desk', 'price' => 200],
    11 => ['product' => 'Desk', 'price' => 200]
]);

$values = $collection->values();

$values->all();

/*
    [
        0 => ['product' => 'Desk', 'price' => 200],
        1 => ['product' => 'Desk', 'price' => 200],
    ]
*/
```

#### `when()`

The `when` method will execute the given callback when the first argument given to the method evaluates to `true`:

```php
$collection = collect([1, 2, 3]);

$collection->when(true, function ($collection) {
    return $collection->push(4);
});

$collection->when(false, function ($collection) {
    return $collection->push(5);
});

$collection->all();

// [1, 2, 3, 4]
```

For the inverse of `when`, see the [`unless`](#unless) method.

#### `whenEmpty()`

The `whenEmpty` method will execute the given callback when the collection is empty:

```php
$collection = collect(['michael', 'tom']);

$collection->whenEmpty(function ($collection) {
    return $collection->push('adam');
});

$collection->all();

// ['michael', 'tom']


$collection = collect();

$collection->whenEmpty(function ($collection) {
    return $collection->push('adam');
});

$collection->all();

// ['adam']


$collection = collect(['michael', 'tom']);

$collection->whenEmpty(function ($collection) {
    return $collection->push('adam');
}, function ($collection) {
    return $collection->push('taylor');
});

$collection->all();

// ['michael', 'tom', 'taylor']
```

For the inverse of `whenEmpty`, see the [`whenNotEmpty`](#whennotempty) method.

#### `whenNotEmpty()`

The `whenNotEmpty` method will execute the given callback when the collection is not empty:

```php
$collection = collect(['michael', 'tom']);

$collection->whenNotEmpty(function ($collection) {
    return $collection->push('adam');
});

$collection->all();

// ['michael', 'tom', 'adam']


$collection = collect();

$collection->whenNotEmpty(function ($collection) {
    return $collection->push('adam');
});

$collection->all();

// []


$collection = collect();

$collection->whenNotEmpty(function ($collection) {
    return $collection->push('adam');
}, function ($collection) {
    return $collection->push('taylor');
});

$collection->all();

// ['taylor']
```

For the inverse of `whenNotEmpty`, see the [`whenEmpty`](#whenempty) method.

#### `where()`

The `where` method filters the collection by a given key / value pair:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
    ['product' => 'Bookcase', 'price' => 150],
    ['product' => 'Door', 'price' => 100],
]);

$filtered = $collection->where('price', 100);

$filtered->all();

/*
    [
        ['product' => 'Chair', 'price' => 100],
        ['product' => 'Door', 'price' => 100],
    ]
*/
```

The `where` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`whereStrict`](#wherestrict) method to filter using "strict" comparisons.

Optionally, you may pass a comparison operator as the second parameter.

```php
$collection = collect([
    ['name' => 'Jim', 'deleted_at' => '2019-01-01 00:00:00'],
    ['name' => 'Sally', 'deleted_at' => '2019-01-02 00:00:00'],
    ['name' => 'Sue', 'deleted_at' => null],
]);

$filtered = $collection->where('deleted_at', '!=', null);

$filtered->all();

/*
    [
        ['name' => 'Jim', 'deleted_at' => '2019-01-01 00:00:00'],
        ['name' => 'Sally', 'deleted_at' => '2019-01-02 00:00:00'],
    ]
*/
```

#### `whereStrict()`

This method has the same signature as the [`where`](#where) method; however, all values are compared using "strict" comparisons.

#### `whereBetween()`

The `whereBetween` method filters the collection within a given range:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 80],
    ['product' => 'Bookcase', 'price' => 150],
    ['product' => 'Pencil', 'price' => 30],
    ['product' => 'Door', 'price' => 100],
]);

$filtered = $collection->whereBetween('price', [100, 200]);

$filtered->all();

/*
    [
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Door', 'price' => 100],
    ]
*/
```

#### `whereIn()`

The `whereIn` method filters the collection by a given key / value contained within the given array:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
    ['product' => 'Bookcase', 'price' => 150],
    ['product' => 'Door', 'price' => 100],
]);

$filtered = $collection->whereIn('price', [150, 200]);

$filtered->all();

/*
    [
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Bookcase', 'price' => 150],
    ]
*/
```

The `whereIn` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`whereInStrict`](#whereinstrict) method to filter using "strict" comparisons.

#### `whereInStrict()`

This method has the same signature as the [`whereIn`](#wherein) method; however, all values are compared using "strict" comparisons.

#### `whereInstanceOf()`

The `whereInstanceOf` method filters the collection by a given class type:

```php
use App\User;
use App\Post;

$collection = collect([
    new User,
    new User,
    new Post,
]);

$filtered = $collection->whereInstanceOf(User::class);

$filtered->all();

// [App\User, App\User]
```

#### `whereNotBetween()`

The `whereNotBetween` method filters the collection within a given range:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 80],
    ['product' => 'Bookcase', 'price' => 150],
    ['product' => 'Pencil', 'price' => 30],
    ['product' => 'Door', 'price' => 100],
]);

$filtered = $collection->whereNotBetween('price', [100, 200]);

$filtered->all();

/*
    [
        ['product' => 'Chair', 'price' => 80],
        ['product' => 'Pencil', 'price' => 30],
    ]
*/
```

#### `whereNotIn()`

The `whereNotIn` method filters the collection by a given key / value not contained within the given array:

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
    ['product' => 'Bookcase', 'price' => 150],
    ['product' => 'Door', 'price' => 100],
]);

$filtered = $collection->whereNotIn('price', [150, 200]);

$filtered->all();

/*
    [
        ['product' => 'Chair', 'price' => 100],
        ['product' => 'Door', 'price' => 100],
    ]
*/
```

The `whereNotIn` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`whereNotInStrict`](#wherenotinstrict) method to filter using "strict" comparisons.

#### `whereNotInStrict()`

This method has the same signature as the [`whereNotIn`](#wherenotin) method; however, all values are compared using "strict" comparisons.

#### `whereNotNull()`

The `whereNotNull` method filters items where the given key is not null:

```php
$collection = collect([
    ['name' => 'Desk'],
    ['name' => null],
    ['name' => 'Bookcase'],
]);

$filtered = $collection->whereNotNull('name');

$filtered->all();

/*
    [
        ['name' => 'Desk'],
        ['name' => 'Bookcase'],
    ]
*/
```

#### `whereNull()`

The `whereNull` method filters items where the given key is null:

```php
$collection = collect([
    ['name' => 'Desk'],
    ['name' => null],
    ['name' => 'Bookcase'],
]);

$filtered = $collection->whereNull('name');

$filtered->all();

/*
    [
        ['name' => null],
    ]
*/
```

#### `wrap()`

The static `wrap` method wraps the given value in a collection when applicable:

```php
$collection = Collection::wrap('John Doe');

$collection->all();

// ['John Doe']

$collection = Collection::wrap(['John Doe']);

$collection->all();

// ['John Doe']

$collection = Collection::wrap(collect('John Doe'));

$collection->all();

// ['John Doe']
```

#### `zip()`

The `zip` method merges together the values of the given array with the values of the original collection at the corresponding index:

```php
$collection = collect(['Chair', 'Desk']);

$zipped = $collection->zip([100, 200]);

$zipped->all();

// [['Chair', 100], ['Desk', 200]]
```

## Higher Order Messages

Collections also provide support for "higher order messages", which are short-cuts for performing common actions on collections. The collection methods that provide higher order messages are: [`average`](#average), [`avg`](#avg), [`contains`](#contains), [`each`](#each), [`every`](#every), [`filter`](#filter), [`first`](#first), [`flatMap`](#flatmap), [`groupBy`](#groupby), [`keyBy`](#keyby), [`map`](#map), [`max`](#max), [`min`](#min), [`partition`](#partition), [`reject`](#reject), [`some`](#some), [`sortBy`](#sortby), [`sortByDesc`](#sortbydesc), [`sum`](#sum), and [`unique`](#unique).

Each higher order message can be accessed as a dynamic property on a collection instance. For instance, let's use the `each` higher order message to call a method on each object within a collection:

```php
$users = User::where('votes', '>', 500)->get();

$users->each->markAsVip();
```

Likewise, we can use the `sum` higher order message to gather the total number of "votes" for a collection of users:

```php
$users = User::where('group', 'Development')->get();

return $users->sum->votes;
```

## Lazy Collections

> **NOTE:** Before learning more about Winter's lazy collections, take some time to familiarize yourself with [PHP generators](https://www.php.net/manual/en/language.generators.overview.php).

To supplement the already powerful `Collection` class, the `LazyCollection` class leverages PHP's [generators](https://www.php.net/manual/en/language.generators.overview.php) to allow you to work with very large datasets while keeping memory usage low.

For example, imagine your application needs to process a multi-gigabyte log file while taking advantage of Winter's collection methods to parse the logs. Instead of reading the entire file into memory at once, lazy collections may be used to keep only a small part of the file in memory at a given time:

```php
use App\LogEntry;
use Illuminate\Support\LazyCollection;

LazyCollection::make(function () {
    $handle = fopen('log.txt', 'r');

    while (($line = fgets($handle)) !== false) {
        yield $line;
    }
})->chunk(4)->map(function ($lines) {
    return LogEntry::fromLines($lines);
})->each(function (LogEntry $logEntry) {
    // Process the log entry...
});
```

Or, imagine you need to iterate through 10,000 Eloquent models. When using traditional Winter collections, all 10,000 Eloquent models must be loaded into memory at the same time:

```php
$users = App\User::all()->filter(function ($user) {
    return $user->id > 500;
});
```

However, the query builder's `cursor` method returns a `LazyCollection` instance. This allows you to still only run a single query against the database but also only keep one Eloquent model loaded in memory at a time. In this example, the `filter` callback is not executed until we actually iterate over each user individually, allowing for a drastic reduction in memory usage:

```php
$users = App\User::cursor()->filter(function ($user) {
    return $user->id > 500;
});

foreach ($users as $user) {
    echo $user->id;
}
```

### Creating Lazy Collections

To create a lazy collection instance, you should pass a PHP generator function to the collection's `make` method:

```php
use Illuminate\Support\LazyCollection;

LazyCollection::make(function () {
    $handle = fopen('log.txt', 'r');

    while (($line = fgets($handle)) !== false) {
        yield $line;
    }
});
```

### The Enumerable Contract

Almost all methods available on the `Collection` class are also available on the `LazyCollection` class. Both of these classes implement the `Illuminate\Support\Enumerable` contract, which defines the following methods:

<div class="columned-list">

- [all](#all)
- [average](#average)
- [avg](#avg)
- [chunk](#chunk)
- [chunkWhile](#chunkwhile)
- [collapse](#collapse)
- [collect](#collect)
- [combine](#combine)
- [concat](#concat)
- [contains](#contains)
- [containsStrict](#containsstrict)
- [count](#count)
- [countBy](#countby)
- [crossJoin](#crossjoin)
- [dd](#dd)
- [diff](#diff)
- [diffAssoc](#diffassoc)
- [diffKeys](#diffkeys)
- [dump](#dump)
- [duplicates](#duplicates)
- [duplicatesStrict](#duplicatesstrict)
- [each](#each)
- [eachSpread](#eachspread)
- [every](#every)
- [except](#except)
- [filter](#filter)
- [first](#first)
- [firstOrFail](#firstorfail)
- [firstWhere](#firstwhere)
- [flatMap](#flatmap)
- [flatten](#flatten)
- [flip](#flip)
- [forPage](#forpage)
- [get](#get)
- [groupBy](#groupby)
- [has](#has)
- [implode](#implode)
- [intersect](#intersect)
- [intersectByKeys](#intersectbykeys)
- [isEmpty](#isempty)
- [isNotEmpty](#isnotempty)
- [join](#join)
- [keyBy](#keyby)
- [keys](#keys)
- [last](#last)
- [macro](#macro)
- [make](#make)
- [map](#map)
- [mapInto](#mapinto)
- [mapSpread](#mapspread)
- [mapToGroups](#maptogroups)
- [mapWithKeys](#mapwithkeys)
- [max](#max)
- [median](#median)
- [merge](#merge)
- [mergeRecursive](#mergerecursive)
- [min](#min)
- [mode](#mode)
- [nth](#nth)
- [only](#only)
- [pad](#pad)
- [partition](#partition)
- [pipe](#pipe)
- [pluck](#pluck)
- [random](#random)
- [reduce](#reduce)
- [reject](#reject)
- [replace](#replace)
- [replaceRecursive](#replacerecursive)
- [reverse](#reverse)
- [search](#search)
- [shuffle](#shuffle)
- [skip](#skip)
- [slice](#slice)
- [sole](#sole)
- [some](#some)
- [sort](#sort)
- [sortBy](#sortby)
- [sortByDesc](#sortbydesc)
- [sortKeys](#sortkeys)
- [sortKeysDesc](#sortkeysdesc)
- [split](#split)
- [sum](#sum)
- [take](#take)
- [tap](#tap)
- [times](#times)
- [toArray](#toarray)
- [toJson](#tojson)
- [union](#union)
- [unique](#unique)
- [uniqueStrict](#uniquestrict)
- [unless](#unless)
- [unlessEmpty](#unlessempty)
- [unlessNotEmpty](#unlessnotempty)
- [unwrap](#unwrap)
- [values](#values)
- [when](#when)
- [whenEmpty](#whenempty)
- [whenNotEmpty](#whennotempty)
- [where](#where)
- [whereStrict](#wherestrict)
- [whereBetween](#wherebetween)
- [whereIn](#wherein)
- [whereInStrict](#whereinstrict)
- [whereInstanceOf](#whereinstanceof)
- [whereNotBetween](#wherenotbetween)
- [whereNotIn](#wherenotin)
- [whereNotInStrict](#wherenotinstrict)
- [wrap](#wrap)
- [zip](#zip)

</div>

> **NOTE:** Methods that mutate the collection (such as `shift`, `pop`, `prepend` etc.) are _not_ available on the `LazyCollection` class.

### Lazy Collection Methods

In addition to the methods defined in the `Enumerable` contract, the `LazyCollection` class contains the following methods:

#### `tapEach()`

While the `each` method calls the given callback for each item in the collection right away, the `tapEach` method only calls the given callback as the items are being pulled out of the list one by one:

```php
$lazyCollection = LazyCollection::times(INF)->tapEach(function ($value) {
    dump($value);
});

// Nothing has been dumped so far...

$array = $lazyCollection->take(3)->all();

// 1
// 2
// 3
```

#### `remember()`

The `remember` method returns a new lazy collection that will remember any values that have already been enumerated and will not retrieve them again when the collection is enumerated again:

```php
$users = User::cursor()->remember();

// No query has been executed yet...

$users->take(5)->all();

// The query has been executed and the first 5 users have been hydrated from the database...

$users->take(20)->all();

// First 5 users come from the collection's cache... The rest are hydrated from the database...
```
