# Extra UI Features

- [Introduction](#introduction)
- [Loading indicator](#loader-stripe)
- [Loading button](#loader-button)
- [Flash messages](#ajax-flash)
- [Form validation](#ajax-validation)
    - [Throwing a validation error](#throw-validation-exception)
    - [Displaying error messages](#error-messages)
    - [Displaying errors with fields](#field-errors)
    - [Usage examples](#usage-examples)

<a name="introduction"></a>
## Introduction

When using the Snowboard framework, you have the option to specify the `extras` flag which includes additional UI features. These features are often useful when working with AJAX requests in frontend CMS pages.

```twig
{% snowboard extras %}
```

<a name="loader-stripe"></a>
## Loading indicator

The loading indicator is a loading bar that is displayed on the top of the page when an AJAX request runs. The indicator hooks in to [global events](../snowboard/request#global-events) used by the Snowboard framework.

When an AJAX request starts, the `ajaxPromise` event is fired. This displays the loading indicator at the top of the page. When this promise is resolved, the loading bar is removed.

<a name="loader-button"></a>
## Loading button

When any element contains the `data-attach-loading` attribute, the CSS class `wn-loading` will be added to it during the AJAX request. This class will spawn a *loading spinner* on button and anchor elements using the `:after` CSS selector.

```html
<form data-request="onSubmit">
    <button data-attach-loading>
        Submit
    </button>
</form>

<a
    href="#"
    data-request="onDoSomething"
    data-attach-loading>
    Do something
</a>
```

<a name="ajax-flash"></a>
## Flash messages

Specify the `data-request-flash` attribute on a form to enable the use of flash messages on successful AJAX requests.

```html
<form
    data-request="onSuccess"
    data-request-flash>
    <!-- ... -->
</form>
```

Combined with use of the `Flash` facade in the event handler, a flash message will appear after the request finishes.

```php
function onSuccess()
{
    Flash::success('You did it!');
}
```

When using AJAX Flash messages you should also ensure that your theme supports [standard flash messages](../markup/tag-flash) by placing the following code in your page or layout in order to render Flash messages that haven't been displayed yet when the page loads.

```twig
{% flash %}
    <p
        data-control="flash-message"
        class="flash-message fade {{ type }}"
        data-interval="5">
        {{ message }}
    </p>
{% endflash %}
```

<a name="ajax-validation"></a>
## Form validation

You may specify the `data-request-validate` attribute on a form to enable server-side validation features with fields and forms.

```html
<form
    data-request="onSubmit"
    data-request-validate>
    <!-- ... -->
</form>
```

<a name="throw-validation-exception"></a>
### Throwing a validation error

In the server side AJAX handler, you may throw a [validation exception](../services/error-log#validation-exception) using the `ValidationException` class to make a field invalid. The exception should be provided an array, which states the field names for the keys, and the error messages for the values.

```php
function onSubmit()
{
    throw new ValidationException(['name' => 'You must give a name!']);
}
```

> **NOTE**: You can also pass a [Validator](../services/validation) instance as the first argument of the exception instead, to use the in-built validation service.

<a name="error-messages"></a>
### Displaying error messages

Inside the form, you may display the first error message by using the `data-validate-error` attribute on a container element. The content inside the container will be set to the error message and the element will be made visible.

```html
<div data-validate-error></div>
```

To display multiple error messages, include an element with the `data-message` attribute. In this example the paragraph tag will be duplicated and set with content for each message that exists.

```html
<div class="alert alert-danger" data-validate-error>
    <p data-message></p>
</div>
```

The `handleValidationErrors` callback, and the `ajaxValidationErrors` global event, that are available with the [Request API](../snowboard/request#global-events) allow you to fully customise the client-side validation handling. The `handleValidationErrors` callback can be used to control validation per request, while the `ajaxValidationErrors` global event can be used by [Snowboard plugins](../snowboard/plugin-development) to augment the client-side validation in a global fashion.

<a name="field-errors"></a>
### Displaying errors with fields

Alternatively, you can show validation messages for individual fields by defining an element that uses the `data-validate-for` attribute, passing the field name as the value.

```html
<!-- Input field -->
<input name="phone" />

<!-- Validation message for the field -->
<div data-validate-for="phone"></div>
```

If the element is left empty, it will be populated with the validation text from the server. Otherwise you can specify any text you like and it will be displayed instead.

```html
<div data-validate-for="phone">
    Oops.. phone number is invalid!
</div>
```

<a name="usage-examples"></a>
### Usage examples

Below is a complete example of form validation. It calls the `onDoSomething` event handler that triggers a loading submit button, performs validation on the form fields, then displays a successful flash message.

```html
<form
    data-request="onDoSomething"
    data-request-validate
    data-request-flash>

    <div>
        <input name="name" />
        <span data-validate-for="name"></span>
    </div>

    <div>
        <input name="email" />
        <span data-validate-for="email"></span>
    </div>

    <button data-attach-loading>
        Submit
    </button>

    <div class="alert alert-danger" data-validate-error>
        <p data-message></p>
    </div>

</form>
```

The AJAX event handler looks at the POST data sent by the client and applies some rules to the validator. If the validation fails, a `ValidationException` is thrown, otherwise a `Flash::success` message is returned.

```php
function onDoSomething()
{
    $data = post();

    $rules = [
        'name' => 'required',
        'email' => 'required|email',
    ];

    $validation = Validator::make($data, $rules);

    if ($validation->fails()) {
        throw new ValidationException($validation);
    }

    Flash::success('Jobs done!');
}
```
