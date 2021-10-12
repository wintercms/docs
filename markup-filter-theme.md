# | theme

The `| theme` filter returns an address relative to the active theme path of the website. The result is an absolute URL, including domain and protocol, to the asset specified in the filter parameter. Theme assets usually reside in the **assets** subdirectory of a theme directory.

```twig
<script type="text/javascript" src="{{ 'assets/js/menu.js' | theme }}"></script>
```

If the website address is __https://example.com__ and the active theme is called `website` the above example would output the following:

```twig
<script type="text/javascript" src="https://example.com/themes/website/assets/js/menu.js"></script>
```

The filter can also be used to interact with the [Asset Compiler](../services/asset-compilation) by passing an array of files which can be used for asset compilation or combination.

```twig
<link href="{{ [
    'assets/css/styles1.css',
    'assets/css/styles2.css'
] | theme }}" rel="stylesheet">
```