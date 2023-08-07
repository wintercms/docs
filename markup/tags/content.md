# {% content %}

The `{% content %}` tag will display a [CMS content block](../../docs/cms/content) on the page. To display content block called **contacts.htm** you pass the file name after the `content` tag quoted as a string.

```twig
{% content "contacts.htm" %}
```

A content block inside a subdirectory can be rendered in the same way.

```twig
{% content "sidebar/content.htm" %}
```

> **NOTE**: The [Themes documentation](../../docs/cms/themes#subdirectories) has more details on subdirectory usage.

Content blocks can be rendered as plain text:

```twig
{% content "readme.txt" %}
```

You can also use Markdown syntax:

```twig
{% content "changelog.md" %}
```

Content blocks can also be used in combination with [layout placeholders](../../docs/cms/layouts#placeholders):

```twig
{% put sidebar %}
    {% content 'sidebar-content.htm' %}
{% endput %}
```

## Variables

You can pass variables to content blocks by specifying them after the file name:

```twig
{% content "welcome.htm" name=user.name %}
```

You can also assign new variables for use in the content:

```twig
{% content "location.htm" city="Vancouver" country="Canada" %}
```

Inside the content, variables can be accessed using a basic syntax using singular *curly brackets*:

```twig
<p>Country: {country}, city: {city}.</p>
```

You can also pass a collection of variables as a simple array:

```twig
{% content "welcome.htm" likes=[
    {name:'Dogs'},
    {name:'Fishing'},
    {name:'Golf'}
] %}
```

The collection of variables is accessed by using an opening and closing set of brackets:

```twig
<ul>
    {likes}
        <li>{name}</li>
    {/likes}
</ul>
```

> **NOTE**: Twig syntax is not supported in Content blocks, consider using a [CMS partial](../../docs/cms/partials) instead.
