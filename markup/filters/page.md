---
title: "Filters: page"
description: "Documentation on the 'page' Twig filter."
---
# page

## Usage

The `| page` filter creates a link to a CMS page, using a page file name without an extension as a parameter. For example, if there is an `about.htm` page in your Winter install, you can use the following code to generate a link to it:

```twig
<a href="{{ 'about' | page }}">About us</a>
```

If your page resides in a subdirectory, you should also specify the subdirectory name:

```twig
<a href="{{ 'about/contact-us' | page }}">Contact us</a>
```

> **NOTE**: The [Themes documentation](../../docs/cms/themes#subdirectories) has more details on subdirectory usage.

To access the link to a certain page from the PHP section, you can use `$this->pageUrl('page-name-without-extension')`:

```php
==
<?php
function onStart() {
    $this['newsPage'] = $this->pageUrl('blog/overview');
}
?>
==
{{ newsPage }}
```

You can create a link to the current page by using an empty string:

```twig
<a href="{{ '' | page }}">Refresh page</a>
```

To get the link to the current page in PHP, you can use `$this->pageUrl('')` with an empty string.

```php
==
<?php
function onStart() {
    $this['currentUrl'] = $this->pageUrl('');
}
?>
==
{{ currentUrl }}
```

## Reverse routing

When linking to a page that has URL parameters defined, the ` | page` filter supports reverse routing by passing an array as the first argument.

For example, if you have a file called `post.htm` with the following URL defined for displaying blog posts:

```ini
url = "/blog/post/:post_id"
==
[...]
```

You can link to a specific blog post by providing the `post_id` parameter to the first argument for the `page` filter:

```twig
<a href="{{ 'post' | page({ post_id: 10 }) }}">
    Blog post #10
</a>
```

If the website address is `https://wintercms.com`, the above example would output the following:

```html
<a href="https://wintercms.com/blog/post/10">
    Blog post #10
</a>
```

## Persistent URL parameters

If a URL parameter is already present and used in the current page, the ` | page` filter will use it automatically.

Using the above example, if you have a file called `edit-post.htm` with the following URL defined for editing blog posts:

```ini
url = "/blog/post/edit/:post_id"
==
[...]
```

You can create a link within the original `post.htm` to this edit page without specifying a post ID to edit the current post:

```twig
<a href="{{ 'post-edit' | page }}">
    Edit this post
</a>
```

This will output the following:

```html
<a href="https://wintercms.com/blog/post/edit/10">
    Edit this post
</a>
```

The `post_id` value of `10` is already known and has persisted across the pages. If you do not want this behaviour to occur, you can disable this functionality by passing `false` as the filter argument:

```twig
<a href="{{ 'post-edit' | page(false) }}">
    Edit blog posts
</a>
```

Or by defining a different value:

```twig
<a href="{{ 'post' | page({ post_id: 6 }) }}">
    Edit post #6
</a>
```
