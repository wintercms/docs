# url_*()

## url()

The `url()` function generates a fully qualified URL to the given path.

```twig
{{ url('blog') }}

{#  https://site.com/blog #}
```

You can specify a variable as a function parameter:

```twig
{{ url(category.slug) }}

{#  https://site.com/slug-value #}
```

In function parameters you can use concatenation:

```twig
{{ url('blog/post/'  ~ post.id) }}

{#  https://site.com/blog/post/123 #}
```

### Base URL

You can get the base url like this:

```twig
{{ url('/') }}

{#  https://site.com/ #}
```

## url_current()

The `url_current()` function generates a fully qualified URL to the current path. Syntax:

```twig
{{ url_current() }}
```

An example of generating a canonical link using the `url_current()` function and the `|lower` Twig filter:

```twig
<link rel="canonical" href="{{ url_current()|lower }}" />
```
