# Updating Partials

When a handler executes it may prepare partials that are updated on the page, either by pushing or pulling, which can be rendered with some supplied variables.

## Pulling partial updates

The client browser may request partials to be updated from the server when it performs an AJAX request, which is considered *pulling a content update*. The following code renders the **mytime** partial inside the `#myDiv` element on the page after calling the `onRefreshTime` [event handler](../ajax/handlers).

```twig
<div id="myDiv">{% partial 'mytime' %}</div>
```

The [data attributes API](../ajax/attributes-api) uses the `data-request-update` attribute.

```html
<!-- Attributes API -->
<button
    data-request="onRefreshTime"
    data-request-update="mytime: '#myDiv'">
    Go
</button>
```

The [JavaScript API](../ajax/javascript-api) uses the `update` configuration option:

```js
// JavaScript API
$.request('onRefreshTime', {
    update: { mytime: '#myDiv' }
})
```

### Update definition

The definition of what should be updated is specified as a JSON-like object where:

- the left side (key) is the **partial name**
- the right side (value) is the **target element** to update

The following will request to update the `#myDiv` element with **mypartial** contents.

```
mypartial: '#myDiv'
```

Multiple partials are separated by commas.

```
firstpartial: '#myDiv', secondpartial: '#otherDiv'
```

If the partial name contains a slash or a dash, it is important to 'quote' the left side.

```
'folder/mypartial': '#myDiv', 'my-partial': '#myDiv'
```

The target element will always be on the right side since it can also be a HTML element in JavaScript.

```
mypartial: document.getElementById('myDiv')
```

### Appending and prepending content

If the selector string is prepended with the `@` symbol, the content received from the server will be appended to the element, instead of replacing the existing content.

```
'folder/append': '@#myDiv'
```

If the selector string is prepended with the `^` symbol, the content will be prepended instead.

```
'folder/append': '^#myDiv'
```

## Pushing partial updates

Comparatively, [AJAX handlers](../ajax/handlers) can *push content updates* to the client-side browser from the server-side. To push an update the handler returns an array where the key is a HTML element to update (using a simple CSS selector) and the value is the content to update.

The following example will update an element on the page with the id **myDiv** using the contents found inside the partial **mypartial**. The `onRefreshTime` handler calls the `renderPartial` method to render the partial contents in PHP.

```php
function onRefreshTime()
{
    return [
        '#myDiv' => $this->renderPartial('mypartial')
    ];
}
```

> **NOTE:** The key name must start with an identifier `#` or class `.` character to trigger a content update.

## Passing variables to partials

Depending on the execution context, an [AJAX event handler](../ajax/handlers) makes variables available to partials differently.

- Use `$this[]` inside a page or layout [PHP section](../cms/themes#php-section).
- Use `$this->page[]` inside a [component class](../plugin/components#ajax-handlers).
- Use `$this->vars[]` in the [backend area](../backend/controllers-ajax#ajax).

These examples will provide the **result** variable to a partial for each context:

```php
// From page or layout PHP code section
$this['result'] = 'Hello world!';

// From a component class
$this->page['result'] = 'Hello world!';

// From a backend controller or widget
$this->vars['result'] = 'Hello world!';
```

This value can then be accessed using Twig in the partial:

```twig
<!-- Hello world! -->
{{ result }}
```

## Handling JavaScript in partials

The AJAX framework writes partial updates by modifying the `innerHTML` property of the partial that is being updated. Due to the security measures implemented in most browsers, no `<script>` tags that are written in this manner will execute. This means that you cannot embed `<script>` tags in your partials.

If you would like to allow JavaScript to manipulate your partial after being loaded, you should add an external JavaScript file loaded through your controller or widget through the [`addJs()`](../backend/widgets#widgets-assets) method and listen to the event that fires when AJAX requests complete.

This will be slightly different depending on whether you are using the original [AJAX framework](../ajax/introduction) or using [Snowboard](../snowboard/introduction).

### jQuery example

```js
(function ($) {
    $(document).on('render', function () {
        // ... Your code to manipulate the partial
    });
})(jQuery);
```

### Snowboard example

```js
Snowboard.on('ajaxDone', () => {
    // ... Your code to manipulate the partial
});
```

> **NOTE:** Both examples above will fire on *any* AJAX request being completed. You must refine your code to focus on the specific partial you wish to target by, for example, checking that a particular element with an ID or class exists. You can also use the [AJAX Framework JS API](../ajax/javascript-api) or [Snowboard JS API](../snowboard/request) to attach to a particular element.
