# {% flash %}

The `{% flash %}` and `{% endflash %}` tags will render any flash messages stored in the user session, set by the `Flash` PHP class. The `message` variable inside will contain the flash message text and the markup inside will repeat for multiple flash messages.

```twig
<ul>
    {% flash %}
        <li>{{ message }}</li>
    {% endflash %}
</ul>
```

You can use the `type` variable that represents the flash message type &mdash; `success`, `error`, `info` or `warning`.

```twig
{% flash %}
    <div class="alert alert-{{ type }}">
        {{ message }}
    </div>
{% endflash %}
```

You can also specify the `type`  to filter flash messages of a given type. The next example will show only `success` messages, if there is an `error` message it won't be displayed.

```twig
{% flash success %}
    <div class="alert alert-success">{{ message }}</div>
{% endflash %}
```

## Setting flash messages

Flash messages can be set by [Components](../cms/components) or inside the page or layout [PHP section](../cms/themes#php-section) with the `Flash` class.

```php
<?php

function onSave()
{
    // Sets a successful message
    Flash::success('Settings successfully saved!');

    // Sets an error message
    Flash::error('Error saving settings');

    // Sets a warning message
    Flash::warning('There was a problem but no worries');

    // Sets an informative message
    Flash::info('Just a heads up about the settings');
}
```
