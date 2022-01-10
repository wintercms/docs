# Migration Guide

- [Introduction](#introduction)
- [Breaking changes](#breaking-changes)
  - [Browser support is more strict](#browser-support)
  - [jQuery is no longer required](#no-jquery)
  - [JavaScript in the HTML data attribute framework is deprecated](#html-callbacks)
  - [AJAX events are triggered as DOM events](#ajax-dom-events)
- [Other changes](#other-changes)
  - [JavaScript AJAX Requests](#js-requests)

<a name="introduction"></a>
## Introduction

While care has been given to ensure that the Snowboard framework covers the entire scope of functionality that the original [AJAX framework](../ajax/introduction) provided, there are subtle differences between the two frameworks. Please take the time to read through this document to ensure that you are across the changes, especially if you intend to upgrade an existing project to use this new framework.

<a name="breaking-changes"></a>
## Breaking changes

<a name="browser-support"></a>
### Browser support is more strict

Snowboard drops support for Internet Explorer, as well as some less-used, or discontinued, browsers such as the Samsung Internet Browser and Opera Mini. The framework targets, at the very least, support for the ECMAScript 2015 (ES2015) JavaScript language.

Our build script is set up to consider the following browsers as compatible with the framework:

- The browser must have at least a 0.5% market share.
- The browser must be within the last 4 released versions of that browser.
- The browser must NOT be Internet Explorer.
- The browser must NOT be discontinued by the developer.

For people who wish to support older browsers such as Internet Explorer, you may continue to use the original [AJAX framework](../ajax/introduction), which is still supported by the Winter maintainer team, but will likely not be receiving any new features going forward.

<a name="no-jquery"></a>
### jQuery is no longer required

We have removed the hard dependency with jQuery, which also means that no jQuery functionality exists in this new framework. If you relied on jQuery being available for your own JavaScript functionality, you must include jQuery yourself in your theme.

<a name="html-callbacks"></a>
### JavaScript in the HTML data attribute framework is deprecated

The original [AJAX framework](../ajax/attributes-api#data-attributes) allowed for arbitrary JavaScript code to be specified within the callback data attributes, for example, `data-request-success`, `data-request-error` and `data-request-complete`, as a way of allowing JavaScript to run additional tasks depending on the success or failure of an AJAX request made through the HTML data attributes.

We have dropped support of this feature due to its use of the `eval()` method in JavaScript to execute this JavaScript, which has security implications (especially on front-facing code) and prevents people from using content security policies on their sites without the use of the `unsafe-eval` [CSP rule](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src).

If you wish to use JavaScript with the AJAX functionality, you must either use the [JavaScript Request functionality](../snowboard/request), or use the original [AJAX framework](../ajax/introduction) which retains this feature.

<a name="ajax-dom-events"></a>
### AJAX events are triggered as DOM events

Previously, the original AJAX framework used jQuery's Event system to trigger events on elements that are affected by an AJAX request. As jQuery is no longer used, we now use DOM events in their place.

This change requires us to provide event data as properties of the DOM event, not as handler parameters.

For example, the `ajaxAlways` event which is triggered on an element when an AJAX request is triggered on an element could have a listener set up through jQuery as follows:

```js
$('#element').on('ajaxAlways', function (event, context, data, status, xhr) {
    console.log(context); // The Request's context
    console.log(data); // Data returned from the AJAX response
});
```

Now, you must look at the Event object properties for this information:

```js
$('#element').on('ajaxAlways', function (event) {
    console.log(event.request); // The Request object
    console.log(event.responseData); // Data returned from the AJAX response
});
```

Please review the [JavaScript Request](../snowboard/request) documentation for information on what properties are available for DOM events.

<a name="other-changes"></a>
## Other changes

<a name="js-requests"></a>
### JavaScript AJAX Requests

#### Making a request

The original framework used a jQuery extension to call AJAX requests via JavaScript:

```js
$('#element').request('onAjaxHandler')
```

This is now changed to use the base Winter class to call the Request extension:

```js
Snowboard.request('#element', 'onAjaxHandler');
```