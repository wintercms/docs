# AJAX Requests (JavaScript API)

## Introduction

Snowboard provides core AJAX functionality via the `Request` Snowboard plugin. The `Request` plugin provides powerful flexibility and reusability in making AJAX Requests with the Backend functionality in Winter. It can be loaded by adding the following tag into your CMS Theme's page or layout:

```twig
{% snowboard request %}
```

And called using the following code in your JavaScript:

```js
Snowboard.request('#element', 'onAjax', {});
```

The base `Request` plugin uses the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) provided in most modern browsers to execute AJAX requests from the frontend to the backend in Winter.

>**NOTE:** If you would like to replace any part of the functionality of the base `Request` plugin then you can write a custom [Snowboard Plugin](plugin-development) that extends and overrides the base `Request` plugin to customize it as desired.

The `request` method takes three parameters:

Parameter | Required | Description
--------- | -------- | -----------
`element` | No | The element that this AJAX request is targeting, either as a `HTMLElement` instance, or as a CSS-selector string. This can be any element, but usually will be used with a `form` element.
`handler` | **Yes** | The AJAX handler to call. This should be in the format `on<Handler>`.
`options` | No | The [AJAX request options](#available-options), as an object.

## Request workflow

AJAX requests made through the `Request` class go through the following process:

- The request is initialized and validated with the given element and options.
- If `browserValidate` is enabled and the AJAX request is done with a form, browser-side validation occurs. If this fails, the request is cancelled.
- If `confirm` is specified, a confirmation is presented to the user. If they cancel, the request is cancelled.
- The data provided to the Request, along with any form data if applicable, will be compiled.
- The AJAX request is sent to the Backend context using the [given handler](../snowboard/handlers).
- A response is received from the Backend context and processed, from here, one of three things happen:
    - If the response is successful, then any partials that are instructed to be updated will be updated at this point.
    - If the response is unsuccessful due to a validation error, then a validation message will be shown and the failing fields will be highlighted.
    - If the response is unsuccessful due to any other error, then an error message will be shown.
- The request is then complete.

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
`fetchOptions` | `Object` | If specified, this will override the options used with the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/fetch) to make the request.

The following callbacks may also be specified in the `options` parameter. All callbacks expect a function to be provided. The `this` keyword inside all callbacks will be assigned the `Request` instance that represents the current AJAX request.

Callback | Description
-------- | -----------
`beforeUpdate` | Executes before page elements are updated with the response data. The function receives one parameter: the response data from the AJAX response as an object.
`success` | Executes when the AJAX request is successfully responded to. The function receives two parameters: the response data from the AJAX response as an object, and the `Request` instance.
`error` | Executes when the AJAX request fails due to a server-side error or validation error. The function receives two parameters: the response data from the AJAX response as an object, and the `Request` instance.
`complete` | Executes when the AJAX request is complete, regardless of success or failure. The function receives two parameters: the response data from the AJAX response as an object, and the `Request` instance.

Finally, the following option parameters define override functionality for various actions that the `Request` instance may take during the processing of a response. As with the callback methods, these must be provided as a function.

Option | Parameters | Description
------ | ---------- | -----------
`handleConfirmMessage` | `(string) confirmationMessage` | Handles any confirmations requested of the user.
`handleErrorMessage` | `(string) errorMessage` | Handles any errors occuring during the request
`handleValidationMessage` | `(string) message, (Object) fieldMessages` | Handles validation errors occurring during the request. `fieldMessages` has field names as the key and messages as the value.
`handleFlashMessage` | `(string) message, (string) type` | Handles flash messages.
`handleRedirectResponse` | `(string) redirectUrl` | Handles redirect responses.

## Global events

The `Request` class fires several global events which can be used by plugins to augment or override the functionality of the `Request` class. [Snowboard plugins](../snowboard/plugin-development.md) can be configured to listen to, and act upon, these events by using the `listen()` method to direct the event to a method with the plugin class.

```js
class HandleFlash extends Snowboard.Singleton
{
    /**
     * Defines listeners for global events.
     *
     * @returns {Object}
     */
    listens() {
        return {
            // when the "ajaxFlashMessages" event is called, run the "doFlashMessage" method in this class
            ajaxFlashMessages: 'doFlashMessages',
        };
    }

    doFlashMessages(messages) {
        Object.entries(messages).forEach((entry) => {
            const [cssClass, message] = entry;

            showFlash(message, cssClass);
        });
    }
}
```

Some events are called as Promise events, which means that your listener must itself return a [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) this is either resolved or rejected.

```js
class ConfirmEverything extends Snowboard.Singleton
{
    /**
     * Defines listeners for global events.
     *
     * @returns {Object}
     */
    listens() {
        return {
            // when the "ajaxConfirmMessage" event is called, run the "confirm" method in this class
            ajaxConfirmMessage: 'confirm',
        };
    }

    // Confirm all confirmation messages
    confirm() {
        return Promise.resolve(true);
    }
}
```

The following events are called during the Request process:

Event | Promise? | Parameters | Description
----- | -------- | ---------- | -----------
`ajaxSetup` | No | `(Request) request` | Called after the Request is initialized and checked that it can be called. It is intended to be used for modifying the Request parameters before sending to the server. Returning `false` in any event listeners will cancel the request.
`ajaxConfirmMessage` | Yes | `(Request) request, (string) confirmationMessage` | Called if `confirm` is specified, and the Request is ready to be sent to the server. This allows developers to customise the confirmation process or display. If an event listener rejects the Promise, this will cancel the request.
`ajaxBeforeSend` | No | `(Request) request` | Called immediately before the Request is sent. It can be used for final changes to the Request, or cancelling it prematurely. Returning `false` in any event listeners will cancel the request.
`ajaxFetchOptions` | No | `(Object) fetchOptions, (Request) request` | Called immediately when the `Fetch API` is initialised to make the request. It can be used to modify the Fetch options via a plugin. This event cannot be cancelled.
`ajaxStart` | No | `(Promise) callback, (Request) request` | Called when the Request is sent. This event cannot be cancelled.
`ajaxBeforeUpdate` | Yes | `(mixed) response, (Request) request` | Called when a successful response is returned and partials are going to be updated. It can be used to determine which partials will be updated, or can also be used to cancel the partial updates. If an event listener rejects the Promise, no partials will be updated.
`ajaxUpdate` | No | `(HTMLElement) element, (string) content, (Request) request` | Called when an individual partial is updated. It can be used to make further updates to an element, or handle updates. Note that this event is fired *after* the element is updated. This event cannot be cancelled.
`ajaxUpdateComplete` | No | `(array of HTMLElement) elements, (Request) request)` | Called when the partials are updated. It can be used to determine which partials have been updated. This event cannot be cancelled.
`ajaxSuccess` | No | `(Object) responseData, (Request) request` | Called when a successful response is returned and all partial updating is completed. It can be used to cancel further response handling (ie. redirects, flash messages). Returning `false` in any event listeners will prevent any further response handling from taking place.
`ajaxError` | No | `(Object) responseData, (Request) request` | Called when an unsuccessful response is returned from the AJAX request. It can be used to cancel further error handling. Returning `false` in any event listeners will prevent any further response handling from taking place.
`ajaxRedirect` | No | `(string) redirectUrl, (Request) request` | Called when a redirect is to take place, either from the response or through the `redirect` option. Returning `false` in any event listeners will prevent the redirect from executing.
`ajaxErrorMessage` | No | `(string) message, (Request) request` | Called when an error message is to be shown to the user. Returning `false` in any event listeners will prevent the default error handling (showing an alert to the user) from executing.
`ajaxFlashMessages` | No | `(array of Object) flashMessages, (Request) request` | Called when one or more flash messages are to be shown to the user. There is no default functionality for flash messages, so if no event listeners trigger for this event, no activity will occur.
`ajaxValidationErrors` | No | `(HTMLElement) form, (array) fieldMessages, (Request) request` | Called when a validation error is returned in the response. There is no default functionality for validation errors, so if no event listeners trigger for this event, no activity will occur.

## Element events

In addition to global events, local events are fired on elements that trigger an AJAX request. These events are treated as [DOM events](https://developer.mozilla.org/en-US/docs/Web/API/Event) and thus can be listened to by normal DOM event listeners or your framework of choice. The `Request` class will inject properties in the event depending on the type of event and the `event.request` property will always be the `Request` instance.

```js
const element = document.getElementById('my-button');
element.addEventListener('ajaxAlways', (event) => {
    console.log(event.request); // The Request instance
    console.log(event.responseData); // The raw response data as an object
    console.log(event.responseError); // An error object if the response failed
});
```

In most cases, you can cancel the event, and thus the Request, by adding `event.preventDefault()` to your callback.

```js
const element = document.getElementById('my-button');
element.addEventListener('ajaxSetup', (event) => {
    // Never process a request for this element
    event.preventDefault();
});
```

The following events are called during the Request process directly on the element that triggered the request:

Event | Description
----- | -----------
`ajaxSetup` | Called after the Request is initialized and checked that it can be called.
`ajaxPromise` | Called when the AJAX request is sent to the server. A property called `promise` is provided, which is the Promise that is resolved or rejected when the response succeeds or fails.
`ajaxUpdate` | Called when an element is updated by a partial update from an AJAX response. **This event is fired on the element that is updated, not the triggering element.** and thus does not get given the `Request` instance. A property called `content` is provided with the content that was added to this element.
`ajaxDone` | Called when this element makes a successful AJAX request. A property called `responseData` is provided with the raw response data, as an object.
`ajaxFail` | Called when this element makes an unsuccessful AJAX request. A property called `responseError` is provided with the error object.
`ajaxAlways` | Called when an AJAX request is completed, regardless of success or failure.  It is provided two properties: `responseData` and `responseError`, which represent the raw response data and error object, depending on whether the AJAX request succeeded or failed.

## Usage examples

Make a simple request to an AJAX handler without specifying a triggering element.

```js
Snowboard.request(null, 'onAjax');
```

Request a confirmation before submitting a form, and redirect them to a success page.

```js
Snowboard.request('form', 'onSubmit', {
    confirm: 'Are you sure you wish to submit this data?',
    redirect: '/form/success',
});
```

Run a calculation handler and inject some data from the page, then update the total.

```js
Snowboard.request('#calculate', 'onCalculate', {
    data: {
        firstValue: document.getElementById('first-value').value,
        secondValue: document.getElementById('second-value').value,
    },
    update: {
        totalResult: '.total-result'
    },
});
```

Run a calculation handler and show a success message when calculated.

```js
Snowboard.request('#calculate', 'onCalculate', {
    data: {
        firstValue: document.getElementById('first-value').value,
        secondValue: document.getElementById('second-value').value,
    },
    success: (data) => {
        const total = data.total;
        alert('The answer is ' + total);
    },
});
```

Prompt a user to confirm a redirect.

```js
Snowboard.request('form', 'onSubmit', {
    handleRedirectResponse: (url) => {
        if (confirm('Are you sure you wish to go to this URL: ' + url)) {
            window.location.assign(url);
        } else {
            alert('Redirect cancelled');
        }
    },
});
```

Track when an element is updated from an AJAX request.

```js
const element = document.getElementById('updated-element');
element.addEventListener('ajaxUpdate', (event) => {
    console.log('The "updated-element" event was updated with the following content:', event.content);
});
```

Disable file uploads globally from AJAX requests by modifying the `Request` instance.

```js
// In your own Snowboard plugin
class DisableFileUploads extends Snowboard.Singleton
{
    listens() {
        return {
            ajaxSetup: 'stopFileUploads',
        };
    }

    stopFileUploads(request) {
        request.options.files = false;
    }
}
```

## Extending or replacing the Request class

As part of making Snowboard an extensible and flexible platform, developers have the option to extend, or replace entirely, the base Request class. This allows developers to use their own preferred platforms and frameworks for executing the AJAX functionality, partial updates, and much more.

For example, if a developer wanted to use the [Axios library](https://github.com/axios/axios) to execute AJAX requests, as opposed to the in-built Fetch API, one could do this by creating their own Snowboard plugin and extending the `Request` class, replacing the `doAjax()` method in their own class:

```js
const axios = require('axios');

class AxiosRequest extends Request
{
    doAjax() {
        // Allow plugins to cancel the AJAX request before sending
        if (this.snowboard.globalEvent('ajaxBeforeSend', this) === false) {
            return Promise.resolve({
                cancelled: true,
            });
        }

        const ajaxPromise = axios({
            method: 'post',
            url: this.url,
            headers: this.headers,
            data: this.data,
        });

        this.snowboard.globalEvent('ajaxStart', ajaxPromise, this);

        if (this.element) {
            const event = new Event('ajaxPromise');
            event.promise = ajaxPromise;
            this.element.dispatchEvent(event);
        }

        return ajaxPromise;
    }
}
```

You could then either replace the `request` plugin with this class, or alias it as something else:

```js
Snowboard.removePlugin('request');
Snowboard.addPlugin('request', AxiosRequest);

// Or run it as an alias
Snowboard.addPlugin('axios', AxiosRequest);
// And call it thusly
Snowboard.axios('#my-element', 'onSubmit');
```

For more information on the best practices with setting up a Snowboard plugin, view the [Plugin Development](../snowboard/plugin-development.md) documentation.
