---
title: "Filters: media"
description: "Documentation on the 'media' Twig filter."
---
# media

## Usage

The `| media` filter returns an address relative to the public URL of the [Media Manager library](/v1.2/docs/cms/mediamanager). The result is a URL to the media file specified before the filter.

```twig
<img src="{{ 'banner.jpg' | media }}" />
```

If the media manager address is `https://cdn.wintercms.com`, the above example would output the following:

```html
<img src="https://cdn.wintercms.com/banner.jpg" />
```
