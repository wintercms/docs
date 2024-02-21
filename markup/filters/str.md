# Strings

Winter CMS provides Twig filters for working with strings. These filters are identical to the global PHP helper functions that are documented in [Helpers > Strings](../../docs/services/helpers#strings) section.

## camel

The `camel` filter converts the given string to camelCase:

```twig
{{ 'test test'|camel }}

{# testTest #}
```

## finish

The `finish` filter adds a single instance of the given value to a string:

```twig
{{ 'test'|finish('/') }}

{# test/ #}
```

## plural

The `plural` filter converts a string to its plural form. This filter currently only supports the English language:

```twig
{{ 'test'|plural }}

{# tests #}
```

## singular

The `singular` filter converts a string to its singular form. This filter currently only supports the English language:

```twig
{{ 'letters'|singular }}

{# letter #}
```

## slug

The `slug` filter generates a URL friendly "slug" from the given string:

```twig
{{ 'test test'|slug }} {# test-test #}

{{ 'test_test'|slug }} {# test-test #}

{{ 'test test'|slug('+') }} {# test+test #}
```

## snake

The `snake` filter converts the given string to snake_case:

```twig
{{ 'test test'|snake }}

{# test_test #}
```

## studly

The `studly` filter converts the given string to StudlyCase:

```twig
{{ 'test test'|studly }}

{# TestTest #}
```
