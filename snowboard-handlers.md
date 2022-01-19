# Server-side Event Handlers

- [Introduction](#introduction)
    - [Calling a handler](#calling-handlers)
    - [Generic handler](#generic-handler)
- [Redirects in AJAX handlers](#redirects-in-handlers)
- [Returning data from AJAX handlers](#returning-data-from-handlers)
- [Throwing an AJAX exception](#throw-ajax-exception)
- [Running code before handlers](#before-handler)

<a name="introduction"></a>
## Introduction

AJAX event handlers are PHP functions that can be defined in the page or layout [PHP section](../cms/themes#php-section) or inside [components](../cms/components) and are used to execute the server-side functionality of an AJAX request made by the [Request API](../snowboard/request) or [Data Attributes API](../snowboard/data-attributes).

Handler method names should be specified with the `on` prefix, followed by the event name in PascalCase - for example, `onMyHandler` or `onCreatePost`.

All handlers support the use of [updating partials](#updating-partials) as part of the AJAX response. This behavior can also be controlled via the `update` option in the [Request API](../snowboard/request) or the `data-request-update` attribute in the [Data Attributes API](../snowboard/data-attributes).

```php
function onSubmitContactForm()
{
    // AJAX handler functionality goes here
}
```

If two handlers with the same name are defined in a page and layout together, the page handler will be executed. The handlers defined in [components](../cms/components) have the lowest priority.

<a name="calling-handlers"></a>
### Calling a handler

Every AJAX request should specify a handler name. When the request is made, the server will search all the registered handlers and run the handler with the highest priority.

```html
<!-- Attributes API -->
<button data-request="onSubmitContactForm">Go</button>

<!-- JavaScript API -->
<script>
    Snowboard.request(null, 'onSubmitContactForm')
</script>
```

If two components register the same handler name, it is advised to prefix the handler with the [component short name or alias](../cms/components#aliases). If a component uses an alias of **mycomponent** the handler can be targeted with `mycomponent::onName`.

```html
<button data-request="mycomponent::onSubmitContactForm">Go</button>
```

You should use the [`__SELF__`](../plugin/components#referencing-self) variable instead of the hard coded alias in order to support multiple instances of your component existing on the same page.

```twig
<form data-request="{{ __SELF__ }}::onCalculate" data-request-update="'{{ __SELF__ }}::calcresult': '#result'">
```
<a name="generic-handler"></a>
### Generic handler

Sometimes you may need to make an AJAX request for the sole purpose of updating page contents by pulling partial updates without executing any code. You may use the `onAjax` handler for this purpose. This handler is available everywhere the AJAX framework can respond.

#### `clock.htm` Partial
```twig
The time is {{ 'now' | date('H:i:s') }}
```

#### `index.htm` Page
```twig
<span id="clock-display">
    {% partial 'clock' %}
</span>
<button
    data-request="onAjax"
    data-request-update="'clock': '#clock-display'"
>
    Check the time
</button>
```

<a name="redirects-in-handlers"></a>
## Redirects in AJAX handlers

If you need to redirect the browser to another location, return the `Redirect` object from the AJAX handler. The framework will redirect the browser as soon as the response is returned from the server.

```php
function onRedirectMe()
{
    return Redirect::to('http://google.com');
}
```

You may also specify a `redirect` in the Request API options, or through the `data-request-redirect` data attribute. This setting will take precedence over any redirect returned in the AJAX response.

<a name="returning-data-from-handlers"></a>
## Returning data from AJAX handlers

You may want to return structured, arbitrary data from your AJAX handlers. If an AJAX handler returns an array, you can access its elements in the `success` callback handler.

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

Then, in JavaScript:

```js
Snowboard.request(this, 'onHandleForm', {
    success: function(data) {
        console.log(data);
    }
});
```

Data returned in this fashion **cannot** be accessed through the [Data Attributes API](../snowboard/data-attributes).

You may also retrieve the data in [several events](../snowboard/request#global-events) that fire as part of the Request lifecycle.

<a name="throw-ajax-exception"></a>
## Throwing an AJAX exception

You may throw an [AJAX exception](../services/error-log#ajax-exception) using the `AjaxException` class to treat the response as an error while retaining the ability to send response contents as normal. Simply pass the response contents as the first argument of the exception.

```php
throw new AjaxException([
    'error' => 'Not enough questions',
    'questionsNeeded' => 2
]);
```

> **NOTE**: When throwing this exception type, [partials will be updated](../ajax/update-partials) as normal.

<a name="before-handler"></a>
## Running code before handlers

Sometimes you may want code to execute before a handler executes. Defining an `onInit` function as part of the [page execution life cycle](../cms/layouts#dynamic-pages) allows code to run before every AJAX handler.

```php
function onInit()
{
    // From a page or layout PHP code section
}
```

You may define an `init` method inside a [component class](../plugin/components#page-cycle-init) or [backend widget class](../backend/widgets).

```php
function init()
{
    // From a component or widget class
}
```
