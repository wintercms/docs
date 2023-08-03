# {% styles %}

The `{% styles %}` tag renders CSS links to stylesheet files injected by the application. The tag is commonly defined in the HEAD section of a page or layout:

```twig
<head>
    ...
    {% styles %}
</head>
```

> **NOTE**: This tag should appear once only in a given page cycle to prevent duplicated references.

## Injecting styles

Links to StyleSheet files can be injected in PHP by anything implementing the `System\Traits\AssetMaker` trait, see the [Asset Compiler](../services/asset-compilation#injecting-page-assets) docs for more information.

You can also inject raw markup to the `{% styles %}` tag by using the **styles** anonymous [placeholder](../cms/layouts#placeholders). Use the `{% put %}` tag in pages or layouts to add content to the placeholder:

```twig
{% put styles %}
    <link href="/themes/demo/assets/css/page.css" rel="stylesheet" />
{% endput %}
```
