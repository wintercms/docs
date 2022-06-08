# AJAX Requests (Data Attributes API)

- [Introduction](#introduction)
- [Available Data Attributes](#available-attributes)
- [Usage Examples](#usage-examples)

<a name="introduction"></a>
## Introduction

The Data Attributes API is the simpler way of embedding AJAX functionality in your themes and plugins, and removes the need to be experienced with JavaScript. While the [JavaScript API](../snowboard/request) has had numerous changes from the original [AJAX framework](../ajax/introduction), the Data Attributes API has remain largely unchanged, despite being powered by the new Snowboard framework under the hood.

It can be loaded by adding the following tag into your CMS Theme's page or layout:

```twig
{% snowboard request attr %}
```

> **NOTE:** As per the [Migration Guide](../snowboard/migration-guide), arbitrary JavaScript is no longer allowed through the Data Attributes API. Thus, the `data-request-before-update`, `data-request-success`, `data-request-error` and `data-request-complete` attributes are no longer supported. Please use the [JavaScript API](../snowboard/request) if you require this functionality.

<a name="available-attributes"></a>
## Available Data Attributes

Triggering an AJAX request from a valid element is as simple as adding the `data-request` attribute to that element. This generally should be done on a button, link, or form. You can also customize the AJAX request using the following attributes:

<style>
    .attributes-table-precessor + table td:first-child,
    .attributes-table-precessor + table td:first-child > * { white-space: nowrap; }
</style>
<div class="attributes-table-precessor"></div>

Attribute | Description
--------- | -----------
`data-request` | Specifies the AJAX handler name to target for the request.
`data-request-confirm` | Specifies the confirmation message to present to the user before proceeding with the request. If the user cancels, the request is not sent.
`data-request-redirect` | Specifies a URL to redirect the browser to, if a successful AJAX response is received.
`data-request-url` | Specifies the URL to send the AJAX request to. By default, this will be the current URL.
`data-request-update` | Specifies a list of partials and page elements (CSS selectors) to update on a successful AJAX response. The format is as follows: `partial: selector, partial: selector`. Usage of quotes is required in most cases: `'partial': 'selector'`. If the selector is prepended with an `@` symbol, the content received from the server will be appended to the element. If the selector is prepended with a `^` symbol, the content will be prepended. Otherwise, received content will replace the original content in the element.
`data-request-data` | Specifies additional data to send with the request to the server. The format is as follows: `'var': 'value', 'var2': 'new value'`. You may also specify this same attribute on any parent elements of the triggering element, and this data will be merged with the parent data (with the triggering data taking preference). It will also be merged with any form data, if this request triggers within a form.
`data-request-form` | Specifies the form that the AJAX request will include its data from. If this is unspecified, the closest form will be used, or if the element itself is a form, then this will be used.
`data-request-flash` | Specifies if flash messages will be accepted from the response.
`data-request-files` | Specifies if file data will be included in the request. This will allow any file inputs in the form to work.
`data-browser-validate` | Specifies if the in-built browser validation will be triggered. If present, the request will be cancelled if the browser validation fails.
`data-track-input` | Specifies if an input will trigger an AJAX request anytime the input changes. An optional number can be specified in this attribute, which represents the amount of milliseconds between any change and the AJAX request triggering.


When the `data-request` attribute is specified for an element, the element triggers an AJAX request when a user interacts with it. Depending on the type of element, the request is triggered on the following events:

Element | Event
------------- | -------------
**Forms** | when the form is submitted.
**Links, buttons** | when the element is clicked.
**Text, number, and password fields** | when the text is changed and only if the `data-track-input` attribute is presented.
**Dropdowns, checkboxes, radios** | when the element is selected.

<a name="data-attribute-examples"></a>
## Usage examples

Trigger the `onCalculate` handler when the form is submitted. Update the element with the identifier "result" with the **calcresult** partial:

```html
<form data-request="onCalculate" data-request-update="calcresult: '#result'">
```

Request a confirmation when the Delete button is clicked before the request is sent:

```html
<form ... >
    ...
    <button data-request="onDelete" data-request-confirm="Are you sure?">Delete</button>
```

Redirect to another page after the successful request:

```html
<form data-request="onLogin" data-request-redirect="/admin">
```

Send a POST parameter `mode` with a value `update`:

```html
<form data-request="onUpdate" data-request-data="mode: 'update'">
```

Send a POST parameter `id` with value `7` across multiple elements:

```html
<div data-request-data="id: 7">
    <button data-request="onDelete">Delete</button>
    <button data-request="onSave">Update</button>
</div>
```

Including [file uploads](../services/request-input#files) with a request:

```html
<form data-request="onSubmit" data-request-files>
    <input type="file" name="photo" accept="image/*" />
    <button type="submit">Submit</button>
</form>
```
