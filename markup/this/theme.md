# this.theme

You can access the current theme object via `this.theme` and it returns the object `Cms\Classes\Theme`, a reference to the [theme customization object](../../docs/themes/development#theme-customization).

## Properties

`this.theme` will provide direct access to form field values, defined by any theme customization. It also has the following properties natively.

### id

Converts the theme directory name to a CSS friendly identifier. If theme customization is being used, this will return the ThemeData record id.

```twig
<body class="theme-{{ this.theme.id }}">
```

`theme.getId()` can also be used to always get a CSS friendly identifier, whether or not theme customization is being used.

If the theme directory was **website** this would generate a class name of `theme-website`.

### config

An array containing all the theme configuration values found in the `theme.yaml` file.

```twig
<meta name="description" content="{{ this.theme.config.description }}">
```
