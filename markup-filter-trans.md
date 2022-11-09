The `| trans` and `| trans_choice` filters translate the value passed in using the applications localization configuration. The localization strings can be loaded by passing the default translation of your string.

```twig
{{ 'I love Winter CMS.' | trans }};
```

or an example using a [language variable](../plugin/localization):

```twig
{{ "acme.demo::lang.string.example" | trans }}
```

Replacing parameters in translation strings is possible by passing an array as the first argument. Every parameter is prefixed with a `:` character.

```twig
{{ ':name loves Winter CMS.' | trans({ name: 'Samuel' }) }}
```

## Pluralization

The `trans_choice` function is used to process pluralized values.

```twig
{{ 'There is one snowflake|There are many snowflakes' | trans_choice(7) }}
```

The second argument can contain the parameters.

```twig
{{ '{1} :value minute ago|[2,*] :value minutes ago' | trans_choice(5, { value: 5 }) }}
```
