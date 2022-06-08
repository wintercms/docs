# Cache

- [Configuration](#configuration)
- [Pre-requisites](#prerequisites)
    - [Database](#database)
    - [Memcached](#memcached)
    - [Redis](#redis)
    - [APCu](#apcu)
    - [DynamoDB](#dynamodb)
- [Cache usage](#cache-usage)
    - [Retrieving items from the cache](#retrieving-items-from-the-cache)
    - [Storing items in the cache](#storing-items-in-the-cache)
    - [Removing items from the cache](#removing-items-from-the-cache)

<a name="configuration"></a>
## Configuration

Winter provides a unified API for various caching systems and the cache configuration is located at `config/cache.php`. In this file you may specify which cache driver you would like used by default throughout your application. Popular caching systems like [Memcached](http://memcached.org) and [Redis](http://redis.io) are supported out of the box.

The cache configuration file also contains various other options, which are documented within the file, so make sure to read over these options. By default, Winter CMS is configured to use the `file` cache driver which stores the serialized, cached objects in the filesystem. For larger applications, it is recommended that you use an in-memory cache such as Memcached or Redis. You may even configure multiple cache configurations for the same driver.

<a name="prerequisites"></a>
## Pre-requisites

<a name="database"></a>
### Database

The `database` cache driver uses the database in lieu of the file system. There is no other configuration required to use this type as the database structure is already available.

Database caching can be less performant than using a dedicated caching system such as Memcached or Redis. For high-traffic or
larger applications, it is recommended to use one of these options instead.

<a name="memcached"></a>
### Memcached

Using the Memcached cache requires the [Memcached PECL package](http://pecl.php.net/package/memcached) to be installed.

The default configuration uses TCP/IP based on [Memcached::addServer](http://php.net/manual/en/memcached.addserver.php):

```php
'memcached' => [
    'driver' => 'memcached',
    'servers' => [
        [
            'host' => '127.0.0.1',
            'port' => 11211,
            'weight' => 100
        ],
    ],
],
```

You may also set the `host` option to a UNIX socket path. If you do this, the `port` option should be set to `0`:

```php
'memcached' => [
    'driver' => 'memcached',
    'servers' => [
        [
            'host' => '/var/run/memcached/memcached.sock',
            'port' => 0,
            'weight' => 100
        ],
    ],
],
```

You can even specify multiple Memcached servers for failover:

```php
'memcached' => [
    'driver' => 'memcached',
    'servers' => [
        [
            'host' => '127.0.0.1',
            'port' => 11211,
            'weight' => 100
        ],
        [
            'host' => '127.0.0.2',
            'port' => 11211,
            'weight' => 90
        ],
    ],
],
```

<a name="redis"></a>
### Redis

> If you wish to use the default **predis** driver, you will need to install the [Drivers plugin](http://github.com/wintercms/wn-driver-plugins).

The Redis configuration for your application is located in the `config/database.php` configuration file. Within this file, you will see a `redis` array containing the Redis servers used by your application:

```php
'redis' => [

    'client' => 'predis',

    'default' => [
        'host'     => '127.0.0.1',
        'password' => null,
        'port'     => 6379,
        'database' => 0,
    ],

],
```

By default, the Redis cache functionality will use the `predis` client package to provide caching abilities through Redis. You may also use `phpredis` as the client, which uses the PHP `redis` extension. This can be [installed through PECL](http://pecl.php.net/package/redis).

When using `predis`, you may define an `options` array value in your Redis connection definition, allowing you to specify a set of Predis [client options](https://github.com/nrk/predis/wiki/Client-Options).

```php
'redis' => [

    'client' => 'predis',
    'options' => [
        'cluster' => 'redis',
        'prefix'  => '',
    ],
    'default' => [
        'host'     => '127.0.0.1',
        'password' => null,
        'port'     => 6379,
        'database' => 0,
    ],

],
```

If your Redis server requires authentication, you may supply a password by adding a `password` configuration item to your Redis server configuration array.

<a name="apcu"></a>
### APCu

The APCu cache driver can be used in conjunction with the [APCu PECL extension](http://pecl.php.net/package/apcu). No configuration is required for this caching system, as this is configured through your `php.ini` configuration.

<a name="dynamodb"></a>
### Amazon DynamoDB

> If you wish to use Amazon DynamoDB as your caching solution, you will need to install the [Drivers plugin](http://github.com/wintercms/wn-driver-plugins).

Amazon DynamoDB provides an API-driven caching system through Amazon Web Services. The configuration for DynamoDB can be found within the `dynamodb` section of the `config/cache.php` file.

```php
'dynamodb' => [
    'driver'   => 'dynamodb',
    'key'      => '',
    'secret'   => '',
    'region'   => 'us-east-1',
    'table'    => 'cache',
    'endpoint' => '',
],
```

<a name="cache-usage"></a>
## Cache usage

While most caching is handled internally by Winter, the `Cache` facade provides some simple methods for caching your own data.

<a name="retrieving-items-from-the-cache"></a>
### Retrieving items from the cache

The `get` method on the `Cache` facade is used to retrieve items from the cache. If the item does not exist in the cache, `null` will be returned. If you wish, you may pass a second argument to the `get` method specifying the custom default value you wish to be returned if the item doesn't exist:

```php
$value = Cache::get('key');

$value = Cache::get('key', 'default');
```

You may even pass a `Closure` as the default value. The result of the `Closure` will be returned if the specified item does not exist in the cache. Passing a Closure allows you to defer the retrieval of default values from a database or other external service:

```php
$value = Cache::get('key', function() {
    return Db::table(...)->get();
});
```

#### Checking for item existence

The `has` method may be used to determine if an item exists in the cache:

```php
if (Cache::has('key')) {
    //
}
```

#### Incrementing / decrementing values

The `increment` and `decrement` methods may be used to adjust the value of integer items in the cache. Both of these methods optionally accept a second argument indicating the amount by which to increment or decrement the item's value:

```php
Cache::increment('key');

Cache::increment('key', $amount);

Cache::decrement('key');

Cache::decrement('key', $amount);
```

#### Retrieve or update

Sometimes you may wish to retrieve an item from the cache, but also store a default value if the requested item doesn't exist. For example, you may wish to retrieve all users from the cache or, if they don't exist, retrieve them from the database and add them to the cache. You may do this using the `Cache::remember` method:

```php
$value = Cache::remember('users', $seconds, function() {
    return Db::table('users')->get();
});
```

If the item does not exist in the cache, the `Closure` passed to the `remember` method will be executed and its result will be placed in the cache.

You may also combine the `remember` and `forever` methods:

```php
$value = Cache::rememberForever('users', function() {
    return Db::table('users')->get();
});
```

#### Retrieve and delete

If you need to retrieve an item from the cache and then delete it, you may use the `pull` method. Like the `get` method, `null` will be returned if the item does not exist in the cache:

```php
$value = Cache::pull('key');
```

<a name="storing-items-in-the-cache"></a>
### Storing items in the cache

You may use the `put` method on the `Cache` facade to store items in the cache. When you place an item in the cache, you will need to specify the number of seconds for which the value should be cached:

```php
Cache::put('key', 'value', $seconds);
```

Instead of passing the number of seconds until the item expires, you may also pass a PHP `DateTime` instance representing the expiration time of the cached item:

```php
$expiresAt = Carbon::now()->addMinutes(10);

Cache::put('key', 'value', $expiresAt);
```

> **NOTE:** We recommend using `DateTime` instances for defining all expiry lengths, in order to ensure compatibility with future versions of Winter CMS.

The `add` method will only add the item to the cache if it does not already exist in the cache store. The method will return `true` if the item is actually added to the cache. Otherwise, the method will return `false`:

```php
Cache::add('key', 'value', $seconds);
```

The `forever` method may be used to store an item in the cache permanently. These values must be manually removed from the cache using the `forget` method:

```php
Cache::forever('key', 'value');
```

<a name="removing-items-from-the-cache"></a>
### Removing items from the cache

You may remove items from the cache using the `forget` method on the `Cache` facade:

```php
Cache::forget('key');
```

You may clear the entire caching using the `flush` method:

```php
Cache::flush();
```

Flushing the cache **does not** respect the cache prefix and will remove all entries from the cache. Consider this carefully when clearing a cache which is shared by other applications.
