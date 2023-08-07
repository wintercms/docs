---
title: "Filters: imageWidth"
description: "Documentation on the 'imageWidth' Twig filter."
---
# imageWidth

## Usage

The `| imageWidth` filter attempts to identify the width, in pixels, of the provided image source.

```twig
{% set resizedImage = 'banner.jpg' | media | resize(1920, 1080) %}
<img src="{{ resizedImage }}" width="{{ resizedImage | imageWidth }}" />
```

> **NOTE:** This filter does not support thumbnail (already resized) versions of FileModels being passed as the image source.

See the [image resizing docs](../../docs/services/image-resizing#available-sources) for more information on what image sources are supported.

> **NOTE:** The image resizing functionality requires a cache driver that persists cache data between requests in order to function, `array` is not a supported cache driver if you wish to use this functionality.
