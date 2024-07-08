# Router Service

## Basic routing

While routing is handled automatically for the [backend controllers](../backend/controllers-ajax) and CMS pages define their own URL routes in their [page configuration](../cms/pages#page-configuration), the router service is useful primarily for defining fixed APIs and end points.

You can define these routes by creating a file named **routes.php** in a same directory as the [plugin registration file](../plugin/registration). The most basic routes simply accept a URI and a `Closure`:

```php
Route::get('/', function () {
    return 'Hello World';
});

Route::post('foo/bar', function () {
    return 'Hello World';
});

Route::put('foo/bar', function () {
    //
});

Route::delete('foo/bar', function () {
    //
});
```

### Registering a route for multiple verbs

Sometimes you may need to register a route that responds to multiple HTTP verbs. You may do so using the `match` method on the `Route` facade:

```php
Route::match(['get', 'post'], '/', function () {
    return 'Hello World';
});
```

You may even register a route that responds to all HTTP verbs using the `any` method:

```php
Route::any('foo', function () {
    return 'Hello World';
});
```

### Generating URLs to routes

You may generate URLs to your routes using the `Url` facade:

```php
$url = Url::to('foo');
```

## Route parameters

### Required parameters

Sometimes you will need to capture segments of the URI within your route, for example, you may need to capture a user's ID from the URL. You may do so by defining route parameters:

```php
Route::get('user/{id}', function ($id) {
    return 'User '.$id;
});
```

You may define as many route parameters as required by your route:

```php
Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
    //
});
```

Route parameters are always encased within singular *curly brackets*. The parameters will be passed into your route's `Closure` when the route is executed.

> **NOTE:** Route parameters cannot contain the `-` character. Use an underscore (`_`) instead.

### Optional parameters

Occasionally you may need to specify a route parameter, but make the presence of that route parameter optional. You may do so by placing a `?` mark after the parameter name:

```php
Route::get('user/{name?}', function ($name = null) {
    return $name;
});

Route::get('user/{name?}', function ($name = 'John') {
    return $name;
});
```

### Regular expression constraints

You may constrain the format of your route parameters using the `where` method on a route instance. The `where` method accepts the name of the parameter and a regular expression defining how the parameter should be constrained:

```php
Route::get('user/{name}', function ($name) {
    //
})->where('name', '[A-Za-z]+');

Route::get('user/{id}', function ($id) {
    //
})->where('id', '[0-9]+');

Route::get('user/{id}/{name}', function ($id, $name) {
    //
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```

## Named routes

Named routes allow you to conveniently generate URLs or redirects for a specific route. You may specify a name for a route using the `as` array key when defining the route:

```php
Route::get('user/profile', ['as' => 'profile', function () {
    //
}]);
```

### Route groups & named routes

If you are using [route groups](#route-groups), you may specify an `as` keyword in the route group attribute array, allowing you to set a common route name prefix for all routes within the group:

```php
Route::group(['as' => 'admin::'], function () {
    Route::get('dashboard', ['as' => 'dashboard', function () {
        // Route named "admin::dashboard"
    }]);
});
```

### Generating URLs to named routes

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the `Url::route` method:

```php
$url = Url::route('profile');

$redirect = Response::redirect()->route('profile');
```

If the route defines parameters, you may pass the parameters as the second argument to the `route` method. The given parameters will automatically be inserted into the URL:

```php
Route::get('user/{id}/profile', ['as' => 'profile', function ($id) {
    //
}]);

$url = Url::route('profile', ['id' => 1]);
```

## Route groups

Route groups allow you to share route attributes across a large number of routes without needing to define those attributes on each individual route. Shared attributes are specified in an array format as the first parameter to the `Route::group` method.

### Sub-domain routing

Route groups may also be used to route wildcard sub-domains. Sub-domains may be assigned route parameters just like route URIs, allowing you to capture a portion of the sub-domain for usage in your route or controller. The sub-domain may be specified using the `domain` key on the group attribute array:

```php
Route::group(['domain' => '{account}.example.com'], function () {
    Route::get('user/{id}', function ($account, $id) {
        //
    });
});
```

### Route prefixes

The `prefix` group array attribute may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:

```php
Route::group(['prefix' => 'admin'], function () {
    Route::get('users', function () {
        // Matches The "/admin/users" URL
    });
});
```

You may also use the `prefix` parameter to specify common parameters for your grouped routes:

```php
Route::group(['prefix' => 'accounts/{account_id}'], function () {
    Route::get('detail', function ($account_id) {
        // Matches The accounts/{account_id}/detail URL
    });
});
```

### Route Middleware

Registering middleware inside your plugin's `boot()` method will register it globally for each request.
If you want to register middleware to one route at a time you should do it like this:

```php
Route::get('info', 'Acme\News@info')->middleware('Path\To\Your\Middleware');
```

For route groups it could be done like this:

```php
Route::group(['middleware' => 'Path\To\Your\Middleware'], function() {
    Route::get('info', 'Acme\News@info');
});
```

If you want to assign a group of middleware to just one route you can it like this

```php
Route::middleware(['Path\To\Your\Middleware'])->group(function() {
    Route::get('info', 'Acme\News@info');
});
```

And finally, if you want to remove middleware from a route you can it like this

```php
Route::withoutMiddleware("Path\To\Removed\Middleware")
    ->get('info', 'Acme\News@info');
```

You can of course add more than one middleware in a group, just one is used in the above examples for convenience.

## Throttle middleware

When using Winter's API middleware, you can remove the default 60 request per minute rate throttle applied to Winter's
API middleware and replace it with your own. For example

```php
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;

RateLimiter::for("infoThrottle", function (Request $request) {
    return Limit::perMinute(120);
});

Route::group(
    [
        "prefix" => "api",
        "middleware" => ["api"],
    ],
    function () {
        Route::withoutMiddleware("throttle:60,1")
            ->middleware("throttle:infoThrottle")
            ->get('info', 'Acme\News@info');
    }
);
```

## Throwing 404 errors

There are two ways to manually trigger a 404 error from a route. First, you may use the `abort` helper. The `abort` helper simply throws a `Symfony\Component\HttpFoundation\Exception\HttpException` with the specified status code:

```php
App::abort(404);
```

Secondly, you may manually throw an instance of `Symfony\Component\HttpKernel\Exception\NotFoundHttpException`.

More information on handling 404 exceptions and using custom responses for these errors may be found in the [errors & logging](../services/error-log) section of the documentation.
