---
title: "Markup: Templating"
description: "Introduction to the markup syntax used in Winter CMS templates."
---

# Templating

Winter uses the [Twig template language](https://twig.symfony.com/doc/3.x/) to provide markup for theme templates, such as those used for layouts, partials and individual pages. Winter also extends Twig with a number of functions, tags, filters and variables to allow you to use the CMS features and access the page environment information inside your templates.

It is recommended that you review the [Twig documentation](https://twig.symfony.com/doc/3.x/) to understand the basics of using Twig. The Markup documentation on Winter CMS will mainly cover the additional Twig functionality and extensions that Winter provides.

## Variables

Template variables are printed on the page using the double curly bracket (`{{ }}`) format.

```twig
{{ variable }}
```

You may also use expressions inside double curly brackets for conditional output.

```twig
{{ isAjax ? 'Yes' : 'No' }}
```

If you wish to concatenate the output, you may do so with the tilde (`~`) character.

```twig
{{ 'Your name: ' ~ first_name ~ ' ' ~ last_name }}
```

If you use a variable that does not exist, by default, a `null` value is returned, which results in no output.

Winter provides "global" variables under the `this` variable, as listed under the [Variables section](../this/page.md).

## Tags

In Twig, tags are used for control and functionality that (generally) are not output to the page. Tags are wrapped with `{% %}` characters.

```twig
{% tag %}
```

Tags provide a more fluent way to describe template logic and implement conditions or flow.

```twig
{% if stormCloudComing %}
    Stay inside
{% else %}
    Go outside and play
{% endif %}
```

For example, the `{% set %}` tag can be used to set variables inside the template, which can then be used later on in the same template.

```twig
{% set activePage = 'blog' %}

My active page: {{ activePage }}
```

Tags can take on many different signatures and are listed under the [Tags section](../tags/page.md).

## Filters

Filters act as modifiers to variables within the same variable tag, and are applied using a pipe symbol (`|`) followed by the filter name.

```twig
{{ 'string' | filter }}
```

Filters can take arguments like a function in PHP.

```twig
{{ price | currency('USD') }}
```

Filters can be applied in succession. Filters are applied from left to right, with the result of the first filter being passed as the input to the second filter, and so on.

```twig
{{ 'Winter Rain' | upper | replace({'Rain': 'Storm'}) }}
```

Filters are listed under the [Filters section](../filters/app.md).

## Functions

Functions can be used within variable tags to display the output of logic that is defined by Winter, the theme or a plugin.

```twig
{{ theFunction() }}
```

Functions can take arguments.

```twig
{{ dump(variable) }}
```

Functions are listed under the [Functions section](../functions/str.md).

## Access logic and priority

The most important thing to learn about Twig is how it accesses the PHP layer and how it prioritises the location of where a particular object or variable is read from. For example, using `{{ foo.bar }}` in your template to get the `bar` parameter of the `foo` object is determined in the following order:

1. Check if `foo` is an array and `bar` a valid element.
1. If not, and if `foo` is an object, check that `bar` is a valid property.
1. If not, and if `foo` is an object, check that `bar` is a valid method (even if bar is the constructor - use `__construct()` instead).
1. If not, and if `foo` is an object, check that `getBar` is a valid method.
1. If not, and if `foo` is an object, check that `isBar` is a valid method.
1. If not, return a `null` value.

## Unsupported features

There are some features offered by Twig that are not supported by Winter. They are listed below next to the equivalent feature.

Tag | Equivalent
------------- | -------------
`{% extend %}` | Use [Layouts](../../docs/cms/layouts) or `{% placeholder %}`
`{% include %}` | Use `{% partial %}` or `{% content %}`
