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
- [Asset Loader](#asset-loader)
- [Data configuration](#data-config)
    - [Example](#data-config-example)
    - [Usage](#data-config-usage)
    - [Further notes](#data-config-notes)

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
        class="flash-message fade"
        data-flash-type="{{ type }}"
        data-flash-duration="5">
        {{ message }}
    </p>
{% endflash %}
```

Flash messages may also be called from your JavaScript files through Snowboard.

```js
Snowboard.flash(
    'This is a flash message', // message, as a string
    'info',                    // flash type, as a string - one of "info", "default", "success", "warning" or "error"
    7                          // duration (seconds), as an integer. Set to 0 to keep flash message until clicked on.
);
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

<a name="asset-loader"></a>
## Asset Loader

Included in the Snowboard extras is an asset loader, allowing simple loading of assets within a page within JavaScript. This loader also allows components to inject assets into your CMS pages when responding to AJAX requests, allowing assets to be deferred until needed.

The following assets can be loaded through the Asset Loader:

- **JavaScript files:** These files will be pre-loaded and injected into the page, before the closing body tag (`</body>`).
- **CSS stylesheets:** These files will be pre-loaded and injected into the page, before the closing head tag (`</head>`)
- **Images:** These files will be pre-loaded but will not be injected into the page, making it useful as an image preloader for images that may be displayed in a component's markup.

By default, the Asset Loader will simply listen for AJAX requests that contain assets in their response, and will automatically load and populate these assets for you as required. However, you can also use this loader to manually inject assets as required:

```js
Snowboard.assetLoader().processAssets({
    js: [
        // URLs of JavaScript files to load, as an array
    ],
    css: [
        // URLs of CSS stylesheet files to load, as an array
    ],
    img: [
        // URLs of images to pre-load, as an array
    ]
});
```

The Asset Loader will ensure that assets are only loaded once - any repeated requests of the same asset will be ignored.

The asset loader fires off two events, depending on whether an asset is successfully loaded or not:

Event | Promise? | Parameters | Description
----- | -------- | ---------- | -----------
`assetLoader.loaded` | No | `(String) type, (String) asset, (HTMLElement) assetElement` | Called when an asset is successfully loaded and injected into the page. The first parameter will be the type of asset (one of `script`, `style` or `image`, the second parameter will be the asset's URL and the third parameter will be HTML element of the injected asset.
`assetLoader.error` | No | `(String) type, (String) asset, (HTMLElement) assetElement` | Called when an asset fails to load. The parameters are the same as the `loaded` event, as the asset will be injected in order to trigger the loading of the asset.

<a name="data-config"></a>
## Data configuration

A common way of including configuration for Winter widgets and Snowboard plugins is to provide an element with data attribute tags that represent the configuration options and values. Snowboard includes a Data Configuration plugin with the extras package that allows you to quickly extract the configuration for a particular plugin from an element's data attributes.

This allows you to pass configuration from the PHP side, such as a component's configuration file, to the partial HTML which can then be read by a corresponding Snowboard plugin on the JavaScript side, allowing a user to manipulate the configuration and experience of a widget entirely through the Winter backend.

<a name="data-config-example"></a>
### Example

Let's say, for example, you have a gallery component that has some configuration options that you pass through to the page when using the component:

```php
namespace Acme\Gallery\Components;

class Gallery extends \Cms\Classes\ComponentBase
{
    public function componentDetails()
    {
        return [
            'name' => 'Gallery',
            'description' => 'My ultra-cool gallery component',
        ];
    }

    public function defineProperties()
    {
        return [
            'numImages' => [
                'title'       => 'Number of images',
                'type'        => 'dropdown',
                'default'     => 3,
                'options' => [
                    1 => 1,
                    2 => 2,
                    3 => 3,
                    4 => 4,
                    5 => 5,
                ]
            ],
            'showCaption' => [
                'title'       => 'Show caption?',
                'type'        => 'checkbox'
                'default'     => true,
            ],
        ];
    }

    public function settings()
    {
        return [
            'numImages' => $this->property('numImages', 3),
            'showCaption' => $this->property('showCaption', true) ? 'true' : 'false',
        ];
    }
}
```

You might then have a default (or overwritten) partial which contains the HTML that will be used by your Snowboard plugin.

```twig
<div
    class="gallery"
    data-gallery
    data-num-images="{{ __SELF__.settings().numImages }}"
    data-show-caption="{{ __SELF__.settings().showCaption }}"
>
    <img src="picture-1.png" title="This is a cool picture">
    <img src="picture-2.png" title="This is another cool picture">
    <img src="picture-3.png" title="Look at this">
    <img src="picture-4.png" title="Wow, so cool!">
    <img src="picture-5.png" title="Nice!">
</div>
```

With this in place, your Snowboard plugin can now initialise a gallery with configuration direct from the Backend.

```js
class Gallery extends Snowboard.PluginBase {

    constructor(snowboard, element) {
        super(snowboard);
        this.element = element;

        // Initialise the configuration, and make it available in the plugin as "this.config"
        this.config = this.snowboard.config(this, element);

        this.createGallery();
    }

    defaults() {
        return {
            numImages: 5,
            showCaption: false,
        };
    }

    createGallery() {
        const numImages = this.config.get('numImages'); // Will return 3 since the attribute has been set by the default value defined in the PHP component class
        const showCaption = this.config.get('showCaption'); // Will be true, since the attribute has been set by the default value defined in the PHP component class
    }
}
```

Following this structure, you have full-stack control over the experience of your component, providing an easy mechanism for controlling the frontend widget from the backend.

<a name="data-config-usage"></a>
### Usage

Initialising a data configuration requires two parameters, the Snowboard plugin that you wish to make the config available to, and a HTML element to extract the data configuration from.

```js
this.config = this.snowboard.config(
    this, // Add the config to the current instance
    element, // HTML element to get the config values from
);
```

By populating it to a plugin variable, you can use it throughout the plugin.

> **NOTE:** The configuration keys (ie. the name from the data attribute name) follow the [name conversion](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset#name_conversion) methodology of the HTML Element dataset. This means, in general, that names will be converted to "camelCase" without the `data-` prefix. In the above example, `data-num-images` is converted to `numImages` within the configuration on the JavaScript side.

When determining the available configuration options, the data configuration will look for a `defaults` method in the plugin instance. This method must return an object that has the accepted configuration options as the object keys, and the default values of these options as the object values.

```js
class Gallery extends Snowboard.PluginBase {

    // ...

    defaults() {
        return {
            numImages: 5,
            showCaption: false,
        };
    }

    // ...

}
```

In the above example, this would allow the `data-num-images` and `data-show-caption` attributes in the given element to populate the configuration, however, if another data attribute (such as `data-allow-zoom`) were added, this would *not* be available as a configuration option.

If you would like to accept any data attribute as a configuration value, you may instead add a property to the plugin instance called `acceptAllDataConfigs` with the value as `true`.

```js
class Gallery extends Snowboard.PluginBase {

    constructor(snowboard, this, element) {
        super(snowboard);

        this.element = element;
        this.acceptAllDataConfigs = true;
        this.config = this.snowboard.config(this, element);
    }

    // ...

}
```

<a name="data-config-methods"></a>
### Methods

The configuration instance that is returned by `this.snowboard.config(bindTo, elementFrom)` provides the following methods:

#### `get()`

Gets the entire configuration as an object, with the configuration name as the object keys and the values as the object values. This object will be made up of the defaults, merged together with the values of the data attributes, which take precedence.

```js
this.config.get(); // Returns an object of all configuration options and their values.
```

#### `get(configName: string)`

Gets the configuration value for the given configuration name. This will be retrieved from the data attribute of the element providing the configuration, or from the the defaults if not specified on the element.

This will return `undefined` if there is no configuration value in the element's data attributes or the defaults.

```js
this.config.get('configKey'); // Returns the value of one configuration option.
```

#### `set(configName: string, configValue: any, persist: boolean)`

Sets a configuration value for the given configuration name at run-time. This will act as an override - it will replace the data configuration and any default for the configuration option.

By default, this override will not persist - if the data configuration is refreshed, it will be replaced by the data attribute value or default once more. If the third parameter `persist` is set to `true`, this value will be persisted, and will be kept even if refreshed.

```js
this.config.set('configKey', 'new value');
this.config.get('configKey'); // Returns "new value".
```

#### `refresh()`

Refreshes the entire configuration. This will repopulate the configuration with the data attribute configuration or default values once more. This can be useful if you allow the data attributes of the element to be modified externally.

```js
// Assuming that "data-config-key" on the element is set to "old"

this.config.set('configKey', 'new');
this.config.get('configKey'); // Returns "new"

// Let's do a refresh
this.config.refresh();
this.config.get('configKey'); // Returns "old", as per the data attribute on the element.

// Let's persist the configuration value.
this.config.set('configKey', 'new', true);

// Another refresh
this.config.refresh();
this.config.get('configKey'); // Returns "new", as the new value was persisted.
```

<a name="data-config-notes"></a>
### Further notes

#### Configuration value coercion

Configuration values provided by a data attribute or through the `set()` method will be "coerced" to a variable type depending on the content, by converting all given contents to a string and applying the following rules:

- A string `"null"` or `"undefined"` will be interpreted as a JavaScript `null` and `undefined`, respectively.
- A string `"true"` or `"yes"` will be interpreted as a boolean `true`.
- A string `"false"` or `"no"` will be interpreted as a boolean `false`.
- A string prefixed with `base64:` followed by a base64-encoded string will be decoded and then run through value coercion with the decoded value.
- A string numeric will be converted to a JavaScript number.
- The strings will be finally be run through a JSON parser - if the parser succeeds, this value will be used.
- If all above fails, the string value is kept as a string.

#### Data attributes without a value

The data configuration interprets a data attribute that has no value to be a boolean `true`, similar to how a checkbox uses the `checked` attribute without a value to make the checkbox checked, or a select option uses the `selected` attribute without a value to make it the selected option.

In cases such as this, it is a good idea to make the default value of the configuration option `false` to make the data attribute a simple toggle.
