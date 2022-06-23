---
title: "Filters: app"
description: "Documentation on the 'app' Twig filter."
---
# app

## Usage

The `| app` filter returns an address relative to the base URL of the website, as defined through the `app.url` configuration value.

The result is an absolute URL, including the domain name and protocol, to the location specified before the filter. The filter can be applied to any path.

>**NOTE**: If an absolute URL is passed to this filter, it will be returned unmodified. Only relative URLs are turned into absolute URLs relative to the web root.

```twig
<link rel="icon" href="{{ '/favicon.ico' | app }}" />
```

For example, if the `app.url` configuration value was set to `https://wintercms.com`, the above example would output the following:

```html
<link rel="icon" href="https://wintercms.com/favicon.ico" />
```

It can also be used for static URLs:

```twig
<a href="{{ '/about-us' | app }}">
    About Us
</a>
```

The above would output:

```html
<a href="https://wintercms.com/about-us">
    About us
</a>
```

The `app` filter is best used for static URLs within a website. For other types of linking, there may be more suitable filters available:

- For linking to static assets outside of a theme, use the [`asset`](asset) filter.
- For linking to assets within a theme, use the [`theme`](theme) filter.
- For CMS pages, the [`page`](page) filter is recommended.
- For media items, the [`media`](media) filter is recommended.

