# Event Handlers

## AJAX handlers

AJAX event handlers are PHP functions that can be defined in the page or layout [PHP section](../cms/themes#php-code-section) or inside [components](../cms/components). Handler names should have the following pattern: `onName`. All handlers support the use of [updating partials](../ajax/update-partials) as part of the AJAX request.

```php
function onSubmitContactForm()
{
    // ...
}
```

If two handlers with the same name are defined in a page and layout together, the page handler will be executed. The handlers defined in [components](../cms/components) have the lowest priority.

### Calling a handler

Every AJAX request should specify a handler name, either using the [data attributes API](../ajax/attributes-api) or the [JavaScript API](../ajax/javascript-api). When the request is made, the server will search all the registered handlers and locate the first one it finds.

```html
<!-- Attributes API -->
<button data-request="onSubmitContactForm">Go</button>

<!-- JavaScript API -->
<script> $.request('onSubmitContactForm') </script>
```

If two components register the same handler name, it is advised to prefix the handler with the [component short name or alias](../cms/components#component-aliases). If a component uses an alias of **mycomponent** the handler can be targeted with `mycomponent::onName`.

```html
<button data-request="mycomponent::onSubmitContactForm">Go</button>
```

You may want to use the [`__SELF__`](../plugin/components#referencing-self) reference variable instead of the hard coded alias in case the user changes the component alias used on the page.

```twig
<form data-request="{{ __SELF__ }}::onCalculate" data-request-update="'{{ __SELF__ }}::calcresult': '#result'">
```

#### Generic handler

Sometimes you may need to make an AJAX request for the sole purpose of updating page contents, not needing to execute any code. You may use the `onAjax` handler for this purpose. This handler is available everywhere without needing to write any code.

```html
<button data-request="onAjax">Do nothing</button>
```

## Redirects in AJAX handlers

If you need to redirect the browser to another location, return the `Redirect` object from the AJAX handler. The framework will redirect the browser as soon as the response is returned from the server. Example AJAX handler:

```php
function onRedirectMe()
{
    return Redirect::to('https://google.com');
}
```

## Returning data from AJAX handlers

In advanced cases you may want to return structured data from your AJAX handlers. If an AJAX handler returns an array, you can access its elements in the `success` event handler. Example AJAX handler:

```php
function onFetchDataFromServer()
{
    /* Some server-side code */

    return [
        'totalUsers' => 1000,
        'totalProjects' => 937
    ];
}
```

The data can be fetched with the data attributes API:

```html
<form data-request="onHandleForm" data-request-success="console.log(data)">
```

The same with the JavaScript API:

```html
<form
    onsubmit="$(this).request('onHandleForm', {
        success: function(data) {
            console.log(data);
        }
    }); return false;">
```

## Throwing an AJAX exception

You may throw an [AJAX exception](../services/error-log#ajax-exception) using the `AjaxException` class to treat the response as an error while retaining the ability to send response contents as normal. Simply pass the response contents as the first argument of the exception.

```php
throw new AjaxException([
    'error' => 'Not enough questions',
    'questionsNeeded' => 2
]);
```

> **NOTE**: When throwing this exception type [partials will be updated](../ajax/update-partials) as normal.

## Running code before handlers

Sometimes you may want code to execute before a handler executes. Defining an `onInit` function as part of the [page execution life cycle](../cms/layouts#layout-execution-life-cycle) allows code to run before every AJAX handler.

```php
function onInit()
{
    // From a page or layout PHP code section
}
```

You may define an `init` method inside a [component class](../plugin/components#component-initialization) or [backend widget class](../backend/widgets).

```php
function init()
{
    // From a component or widget class
}
```
