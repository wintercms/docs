# Developing Themes

The theme directory could include the **theme.yaml**, **version.yaml** and **assets/images/theme-preview.png** files. These files are optional for the local development but required for themes published on the Winter CMS Marketplace.

## Theme information file

The theme information file **theme.yaml** contains the theme description, the author name, URL of the author's website and some other information. The file should be placed to the theme root directory:

```treeview
themes/
`-- example-theme/
       `-- theme.yaml    # Theme information file
```

The following fields are supported in the **theme.yaml** file:

Field | Description
------------- | -------------
`name` | specifies the theme name, required.
`author` | specifies the author name, required.
`homepage` | specifies the author website URL, required.
`description` | the theme description, required.
`previewImage` | custom preview image, path relative to the theme directory, eg: `assets/images/preview.png`, optional.
`code` | the theme code, optional. The value is used on the Winter CMS marketplace for initializing the theme code value. If the theme code is not provided, the theme directory name will be used as a code. When a theme is installed from the Marketplace, the code is used as the new theme directory name.
`form` | a configuration array or reference to a form field definition file, used for [theme customization](#theme-customization), optional.
`require` | an array of plugin names used for [theme dependencies](#theme-dependencies), optional.
`mix` | an object that defines Mix packages contained in your theme for [asset compilation](../console/asset-compilation).

Example of the theme information file:

```yaml
name: "Winter CMS Demo"
description: "Demonstrates the basic concepts of the frontend theming."
author: "Winter CMS"
homepage: "https://wintercms.com"
code: "demo"
```

## Version file

The theme version file **version.yaml** defines the current theme version and the change log. The file should be placed to the theme root directory:

```treeview
themes/
`-- example-theme/
       `-- version.yaml    # Theme version file
```

The file format is following:

```yaml
"v1.0.1": Theme initialization
"v1.0.2": Added more features
"v1.0.3": Some features are removed
```

## Theme preview image

The theme preview image is used in the backend theme selector. The image file **theme-preview.png** should be placed to the theme's **assets/images** directory:

```treeview
themes/
`-- example-theme/
    `-- assets/
       `-- images/
           `-- theme-preview.png    # Theme Preview Image
```

The image width should be at least 600px. The ideal aspect ratio is 1.5, for example 600x400px.

## Theme customization

Themes can support configuration values by defining a `form` key in the theme information file. This key should contain a configuration array or reference to a form field definition file, see [form fields](../backend/forms#defining-form-fields) for more information.

The following is an example of how to define a website name configuration field called **site_name**:

```yaml
name: My Theme
# [...]

form:
    fields:
        site_name:
            label: Site name
            comment: The website name as it should appear on the frontend
            default: My Amazing Site!
```

> **NOTE:** If using nested fields with array syntax (`contact[name]`, `contact[email` etc.) you need to add the top level to the `ThemeData` model's `jsonable` array using the following:

```php
\Cms\Models\ThemeData::extend(function ($model) {
    $model->addJsonable('contact');
});
```

> **NOTE:** When using a `fileupload` field in your theme, Winter will automatically add the field to an `attachOne` relationship. If you want to use multiple file uploads for a field, add the `multiple: true` option to the field definition. This will instead add the field to an `attachMany` relationship.

The value can then be accessed inside any of the Theme templates using the [default page variable](../markup#default-variables) called `this.theme`.

```twig
<h1>Welcome to {{ this.theme.site_name }}!</h1>
```

You may also define the configuration in a separate file, where the path is relative to the theme. The following definition will source the form fields from the file **config/fields.yaml** inside the theme.

```yaml
name: My Theme
# [...]

form: config/fields.yaml
```

**config/fields.yaml**:

```yaml
fields:
    site_name:
        label: Site name
        comment: The website name as it should appear on the frontend
        default: My Amazing Site!
```

### Asset Compiler Variables

Assets combined using the [Asset Compiler](../services/asset-compilation) (usually through thh `| theme` [filter](/docs/v1.2/markup/filters/theme)) can have values passed to supporting filters, such as the LESS filter. Simply specify the `assetVar` option when defining the form field, the value should contain the desired variable name.

```yaml
form:
    fields:
        # [...]

        link_color:
            label: Link color
            type: colorpicker
            assetVar: 'link-color'
```

In the above example, the color value selected will be available inside the less file as `@link-color`. Assuming we have the following stylesheet reference:

```twig
<link href="{{ ['assets/less/theme.less'] | theme }}" rel="stylesheet">
```

Using some example content inside **themes/yourtheme/assets/less/theme.less**:

```less
a { color: @link-color }
```

## Theme dependencies

A theme can depend on plugins by defining a `require` option in the [Theme information file](#theme-information-file), the option should supply an array of plugin names that are considered requirements. A theme that depends on **Acme.Blog** and **Acme.User** can define this requirement like so:

```yaml
name: "Winter CMS Demo"
# [...]

require:
    - Acme.User
    - Acme.Blog
```

When the theme is installed for the first time, the system will attempt to install the required plugins at the same time.

## Localization

Themes can provide backend localization keys through files placed in the **lang** subdirectory of the theme's directory. These localization keys are registered automatically only when interacting with the Winter backend and can be used for form labels just like [plugin localization](../plugin/localization)

> **NOTE**: Translating frontend content should be handled with the [Winter.Translate](https://github.com/wintercms/wn-translate-plugin) plugin.

### Localization directory and file structure

Below is an example of the theme's lang directory:

```treeview
themes/
`-- example-theme/         # Theme directory
    `-- lang/              # Localization directory
       |-- en/             # Specific locale directory
       |   `-- lang.php    # Localization file
       `-- fr/
           `-- lang.php
```

The **lang.php** file should define and return an array of any depth, for example:

```php
<?php return [
    'options' => [
        'website_name' => 'Winter CMS'
    ]
];
```

You are then able to reference the keys using `themes.theme-code::lang.key`. In the above example, the full language key you would use to reference the "website_name" localization key would be `themes.example-theme::lang.options.website_name`
