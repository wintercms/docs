# {% scripts %}

The `{% scripts %}` tag inserts JavaScript file references to scripts injected by the application. The tag is commonly defined before the closing BODY tag:

```twig
<body>
    ...
    {% scripts %}
</body>
```

> **NOTE**: This tag should appear once only in a given page cycle to prevent duplicated references.

## Injecting scripts

Links to JavaScript files can be programmatically injected in PHP by anything implementing the `System\Traits\AssetMaker` trait, see the [Asset Compiler](../services/asset-compilation#injecting-page-assets) docs for more information.

You can also inject raw markup to the `{% scripts %}` tag by using the **scripts**  anonymous [placeholder](../cms/layouts#placeholders). Use the `{% put %}` tag in pages or layouts to add content to the placeholder:

```twig
{% put scripts %}
    <script type="text/javascript" src="/themes/demo/assets/js/menu.js"></script>
{% endput %}
```
