# AJAX Requests (JavaScript API)

<a name="introduction"></a>
## Introduction

Snowboard provides core AJAX functionality via the `Request` JavaScript class. The `Request` class provides powerful flexibility and reusability in making AJAX Requests with the Backend functionality in Winter. It can be loaded by adding the following tag into your CMS Theme's page or layout:

```twig
{% snowboard request %}
```

And can be called using the following code in your JavaScript:

```js
Snowboard.request('#element', 'onAjax', {});
```

The `request` method takes three parameters:

Parameter | Required | Description
--------- | -------- | -----------
`element` | No | The element that this AJAX request is targeting, either as a `HTMLElement` instance, or as a CSS-selector string. This can be any element, but usually will be used with a `form` element.
`handler` | **Yes** | The AJAX handler to call. This should be in the format `on<Handler>`.
`options` | No | The [AJAX request options](#available-options), as an object.

<a name="available-options"></a>
## Available options

All options below are optional.

Option | Type | Description
------ | ---- | -----------
`confirm` | `string` | If provided, the user will be prompted with this confirmation message, and will be required to confirm if they wish to proceed with the request.
`data` | `Object` | Extra data that will be sent to the server along with any form data, if available. If `files` is `true`, you may also include files in the request by using [`Blob` objects](https://developer.mozilla.org/en-US/docs/Web/API/Blob).
`redirect` | `string` | If provided, the browser will be redirected to this URL after a request is successfully completed.
`form` | `HTMLElement` or `string` | Specifies the form that data will be extracted from and sent in the request. If this is not provided, the form will be automatically determined from the element provided with the request. If no element is given, then no form will be used.
`files` | `boolean` | If `true`, this request will accept file uploads in the data.
`browserValidate` | `boolean` | If `true`, the in-built client-side validation provided by most common browsers will be performed before sending the request. This is only applied if a form is used in the request.
`flash` | `boolean` | If `true`, the request will process and display any flash messages returned in the response.
`update` | `Object` | Specifies a list of partials and page elements that can be changed through the AJAX response. The key of the object represents the partial name and the value represents the page element (as a CSS selector) to target for the update. If the selector string is prepended with an `@` symbol, the content will be appended to the target. If the selector string is prepended with a `^` symbol, it will instead be prepended to the target.

The following callbacks may also be specified in the `options` parameter. All callbacks expect a function to be provided. The `this` keyword inside all callbacks will be assigned the `Request` instance that represents this AJAX request.

Callback | Description
-------- | -----------
`beforeUpdate` | Executes before page elements are updated with the response data. The function receives one parameter: the response data from the AJAX response as an object.
`success` | Execures when the AJAX request is successfully responded to. The function receives two parameters: the response data from the AJAX response as an object, and the `Request` instance.
`error` | Executes when the AJAX request fails due to a server-side error or validation error. The function receives two parameters: the response data from the AJAX response as an object, and the `Request` instance.
`complete` | Executes when the AJAX request is complete, regardless of success or failure. The function receives two parameters: the response data from the AJAX response as an object, and the `Request` instance.

Finally, the following option parameters define override functionality for various actions that the `Request` instance may take during the processing of a response. As with the callback methods, these must be provided a function.

Option | Description
------ | -----------
`handleConfirmMessage` | Defines a custom handler for any confirmations requested of the user. It will be provided one parameter: the confirmation message, as a string.
`handleErrorMessage` | Defines a custom handler for any errors occuring during the request. It will be provided one parameter: the error message, as string.
`handleValidationMessage` | Defines a custom handler for validation errors occurring during the request. It will be provided two parameters: the first being the message returned, as a string. The second will be an object, with field names as the key and messages as the value.
`handleFlashMessage` | Defines a custom handler for flash messages. It will be provided two parameters: the message of the flash message as a string, and the type of flash message as a string.
`handleRedirectResponse` | Defines a custom handler for any redirects. It will be provided one parameter: the URL to redirect to, as a string.