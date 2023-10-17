# str_*()

Functions prefixed with `str_` perform tasks that are useful when dealing with strings. The helper maps directly to the `Str` PHP class and its methods. For example:

```twig
{{ str_camel() }}
```

is the PHP equivalent of the following:

```php
<?= Str::camel() ?>
```

> **NOTE**: Methods in *camelCase* should be converted to *snake_case*.

See [Helpers#helpers-string](../../docs/services/helpers#strings) for a list of all available `str_*` helpers.

See [Laravel Helpers](https://laravel.com/docs/9.x/helpers#strings-method-list) for a list of all available `str_*` helpers that come from Laravel. Any helper that matches `Str::$camelCase` is available in Twig as `str_$snake_case` with the same parameters
