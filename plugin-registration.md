# Plugin Registration

- [Introduction](#introduction)
- [Directory structure](#directory-structure)
    - [Simple](#simple-structure)
    - [Typical](#typical-structure)
    - [Complex](#complex-structure)
- [Plugin namespaces](#namespaces)
- [Registration file](#registration-file)
    - [Supported methods](#registration-methods)
    - [Basic plugin information](#basic-plugin-information)
- [Routing and initialization](#routing-initialization)
- [Dependency definitions](#dependency-definitions)
- [Extending Twig](#extending-twig)
- [Navigation menus](#navigation-menus)
- [Registering middleware](#registering-middleware)
- [Elevated permissions](#elevated-plugin)
- [Plugin replacement & forking](replacement)

<div class="og-description">
    Learn how to create and register plugins for Winter CMS.
</div>

<a name="Introduction"></a>
## Introduction

Plugins are the foundation for adding new features or extending the base functionality of Winter CMS. The plugin registration process allows plugins to declare their features such as [components](components), navigation items and Backend pages. Some examples of what a plugin can do:

- Define [components](../plugin/components).
- Define [user permissions](../backend/users).
- Add [settings pages](../plugin/settings#backend-pages), [menu items](../plugin/registration#navigation-menus), [lists](../backend/lists) and [forms](../backend/forms).
- Create [database table structures and seed data](../plugin/updates).
- Alter [functionality of the core or other plugins](../services/events).
- Provide classes, [backend controllers](../backend/controllers-ajax), views, assets, and other files.

<a name="directory-structure"></a>
## Directory structure

Plugins reside in the **/plugins** subdirectory of the application directory. Plugins can range from extremely simple to very complex depending on the requirements. The most simple plugin only requires a `Plugin.php` file, but they can accomodate all the way up to entire application structures as required.

<a name="simple-structure"></a>
### Simple plugin structure

The simplest plugins only require the **Plugin.php** file described below.

```css
📂 plugins
 ┣ 📂 myauthor          /* Author name */
 ┃ ┣ 📂 myplugin        /* Plugin name */
 ┃ ┃ ┗ 📜 Plugin.php    /* Plugin registration file, required */
```

<a name="typical-structure"></a>
### Typical plugin structure

The following is an example of what most plugins would end up looking like when interacting with the most commonly used Winter CMS functionality.

> **NOTE:** if you are developing a plugin for the [Marketplace](/marketplace), the [updates/version.yaml](updates) file is required.

```css
📂 plugins
 ┣ 📂 myauthor              /* Author name */
 ┃ ┣ 📂 myplugin            /* Plugin name */
 ┃ ┃ ┣ 📂 assets            /* CSS, JavaScript and image assets for pages and components */
 ┃ ┃ ┣ 📂 controllers       /* Backend controllers */
 ┃ ┃ ┣ 📂 lang              /* Localization files */
 ┃ ┃ ┃ ┗ 📂 en              /* Specific locale folder */
 ┃ ┃ ┃ ┃ ┗ 📜 lang.php      /* Translations */
 ┃ ┃ ┣ 📂 models            /* Models */
 ┃ ┃ ┣ 📂 updates           /* Database migrations */
 ┃ ┃ ┃ ┗ 📜 version.yaml    /* Changelog */
 ┃ ┃ ┣ 📂 views             /* Custom view files */
 ┃ ┃ ┃ ┗ 📂 mail            /* Custom mail templates */
 ┃ ┃ ┣ 📜 README.md         /* Documentation describing the purpose of the plugin */
 ┃ ┃ ┗ 📜 Plugin.php        /* Plugin registration class */
```

<a name="complex-structure"></a>
### Complex plugin structure

The following is an example of what a complex plugin could look like when using a significant number of the features provided by Winter CMS as well as providing some of its own.

```css
📂 plugins
 ┣ 📂 myauthor                              /* Author name */
 ┃ ┣ 📂 myplugin                            /* Plugin name */
 ┃ ┃ ┣ 📂 assets                            /* CSS, JavaScript and image assets for pages and components */
 ┃ ┃ ┃ ┣ 📂 css
 ┃ ┃ ┃ ┣ 📂 favicons
 ┃ ┃ ┃ ┣ 📂 images
 ┃ ┃ ┃ ┣ 📂 js
 ┃ ┃ ┃ ┗ 📂 scss
 ┃ ┃ ┣ 📂 behaviors                         /* Any custom behaviors provided by the plugin */
 ┃ ┃ ┣ 📂 classes                           /* Any custom classes provided by the plugin */
 ┃ ┃ ┣ 📂 config                            /* Configuration files */
 ┃ ┃ ┃ ┗ 📜 config.php
 ┃ ┃ ┣ 📂 console                           /* Any custom CLI commands provided by the plugin */
 ┃ ┃ ┣ 📂 controllers                       /* Backend controllers */
 ┃ ┃ ┃ ┣ 📂 records                         /* Directory for the view and configuration files for the given controller */
 ┃ ┃ ┃ ┃ ┣ 📜 _list_toolbar.htm             /* List toolbar partial file */
 ┃ ┃ ┃ ┃ ┣ 📜 config_filter.yaml            /* Configuration for the Filter widget present on the controller lists */
 ┃ ┃ ┃ ┃ ┣ 📜 config_form.yaml              /* Configuration for the Form widget present on the controller */
 ┃ ┃ ┃ ┃ ┣ 📜 config_importexport.yaml      /* Configuration for the Import/Export behavior */
 ┃ ┃ ┃ ┃ ┣ 📜 config_list.yaml              /* Configuration for the Lists widget present on the controller */
 ┃ ┃ ┃ ┃ ┣ 📜 config_relation.yaml          /* Configuration for the RelationController behavior */
 ┃ ┃ ┃ ┃ ┣ 📜 create.htm                    /* View file for the create action */
 ┃ ┃ ┃ ┃ ┣ 📜 index.htm                     /* View file for the index action */
 ┃ ┃ ┃ ┃ ┣ 📜 preview.htm                   /* View file for the preview action */
 ┃ ┃ ┃ ┃ ┗ 📜 update.htm                    /* View file for the update action */
 ┃ ┃ ┃ ┣ 📜 Records.php                     /* Backend controller for the Record model */
 ┃ ┃ ┣ 📂 docs                              /* Any plugin-specific documentation should live here */
 ┃ ┃ ┣ 📂 formwidgets                       /* Any custom FormWidgets provided by the plugin */
 ┃ ┃ ┣ 📂 lang                              /* Localization files */
 ┃ ┃ ┃ ┗ 📂 en                              /* Specific locale folder */
 ┃ ┃ ┃ ┃ ┗ 📜 lang.php                      /* Translations for that locale */
 ┃ ┃ ┣ 📂 layouts                           /* Any custom backend layouts used by the plugin */
 ┃ ┃ ┣ 📂 models                            /* Models provided by the plugin */
 ┃ ┃ ┃ ┣ 📂 record                          /* Directory containing configuration files specific to that model */
 ┃ ┃ ┃ ┃ ┣ 📜 columns.yaml                  /* Configuration file used for the Lists widget */
 ┃ ┃ ┃ ┃ ┗ 📜 fields.yaml                   /* Configuration file used for the Form widget */
 ┃ ┃ ┃ ┣ 📜 Record.php                      /* Model class for the Record model */
 ┃ ┃ ┣ 📂 partials                          /* Any custom partials used by the plugin */
 ┃ ┃ ┣ 📂 reportwidgets                     /* Any custom ReportWidgets provided by the plugin */
 ┃ ┃ ┣ 📂 tests                             /* Test suite for the plugin */
 ┃ ┃ ┣ 📂 traits                            /* Any custom Traits provided by the plugin */
 ┃ ┃ ┣ 📂 updates                           /* Database migrations */
 ┃ ┃ ┃ ┃ ┣ 📂 v1.0.0                        /* Migrations for a specific version of the plugin */
 ┃ ┃ ┃ ┃ ┃ ┗ 📜 create_records_table.php    /* Database migration file, referenced in version.yaml */
 ┃ ┃ ┃ ┗ 📜 version.yaml                    /* Changelog */
 ┃ ┃ ┣ 📂 views                             /* Custom view files */
 ┃ ┃ ┃ ┗ 📂 mail                            /* Custom mail templates provided by the plugin */
 ┃ ┃ ┣ 📂 widgets                           /* Any custom Widgets provided by the plugin */
 ┃ ┃ ┣ 📜 LICENSE                           /* License file */
 ┃ ┃ ┣ 📜 README.md                         /* Documentation describing the purpose of the plugin */
 ┃ ┃ ┣ 📜 Plugin.php                        /* Plugin registration file */
 ┃ ┃ ┣ 📜 composer.json                     /* Composer file to manage dependencies for the plugin */
 ┃ ┃ ┣ 📜 helpers.php                       /* Global helpers provided by the plugin loaded via composer.json */
 ┃ ┃ ┣ 📜 phpunit.xml                       /* Unit testing configuration */
 ┃ ┃ ┣ 📜 plugin.yaml                       /* Simplified plugin registration configuration YAML file, used by Builder plugin */
 ┃ ┃ ┗ 📜 routes.php                        /* Any custom routes provided by the plugin */
 ```

<a name="namespaces"></a>
## Plugin namespaces

Plugin namespaces are very important, especially if you are going to publish your plugins on the [Winter Marketplace](https://wintercms.com/marketplace). By using a unique plugin namespace, you remove the chance that your plugin conflicts with another author.

When you register as an author on the Marketplace, you will be asked for the author code which should be used as a root namespace for all your plugins. You can specify the author code only once, when you register.

The default author code offered by the Marketplace consists of the author first and last name: JohnSmith. The code cannot be changed after you register. All your plugin namespaces should be defined under the root namespace, for example `\JohnSmith\Blog`.

<a name="registration-file"></a>
## Registration file

The **Plugin.php** file, called the *Plugin registration file*, is an initialization script that declares a plugin's core functions and information. This file is read in the boot process of Winter CMS when determining available plugins. Registration files can provide the following:

1. Information about the plugin, its name, and author.
1. Registration methods for extending the CMS and stating the intentions of the plugin.

Registration scripts should use the plugin namespace. The registration script should define a class with the name `Plugin` that extends the `\System\Classes\PluginBase` class. The only required method of the plugin registration class is the `pluginDetails` method. An example Plugin registration file:

```php
<?php

namespace Acme\Blog;

class Plugin extends \System\Classes\PluginBase
{
    public function pluginDetails()
    {
        return [
            'name' => 'Blog Plugin',
            'description' => 'Provides some really cool blog features.',
            'author' => 'ACME Corporation',
            'icon' => 'icon-snowflake-o'
        ];
    }

    public function registerComponents()
    {
        return [
            'Acme\Blog\Components\Post' => 'blogPost'
        ];
    }
}
```

<a name="registration-methods"></a>
### Supported methods

The following methods are supported in the plugin registration class:

Method | Description
------------- | -------------
**pluginDetails()** | returns information about the plugin.
**register()** | register method, called when the plugin is first registered.
**boot()** | boot method, called right before the request route.
**registerComponents()** | registers any [frontend components](components#component-registration) used by this plugin.
**registerFormWidgets()** | registers any [backend form widgets](../backend/widgets#form-widget-registration) supplied by this plugin.
**registerListColumnTypes()** | registers any [custom list column types](../backend/lists#custom-column-types) supplied by this plugin.
**registerMailLayouts()** | registers any [mail view layouts](../services/mail#mail-template-registration) supplied by this plugin.
**registerMailPartials()** | registers any [mail view partials](../services/mail#mail-template-registration) supplied by this plugin.
**registerMailTemplates()** | registers any [mail view templates](../services/mail#mail-template-registration) supplied by this plugin.
**registerMarkupTags()** | registers [additional markup tags](#extending-twig) that can be used in the CMS.
**registerNavigation()** | registers [backend navigation menu items](#navigation-menus) for this plugin.
**registerPermissions()** | registers any [backend permissions](../backend/users#permission-registration) used by this plugin.
**registerReportWidgets()** | registers any [backend report widgets](../backend/widgets#report-widget-registration), including the dashboard widgets.
**registerSchedule()** | registers [scheduled tasks](../plugin/scheduling#defining-schedules) that are executed on a regular basis.
**registerSettings()** | registers any [backend configuration links](settings#link-registration) used by this plugin.
**registerValidationRules()** | registers any [custom validators](../services/validation#custom-validation-rules) supplied by this plugin.

<a name="basic-plugin-information"></a>
### Basic plugin information

The `pluginDetails` is a required method of the plugin registration class. It should return an array containing the following keys:

Key | Description
------------- | -------------
**name** | the plugin name, required.
**description** | the plugin description, required.
**author** | the plugin author name, required.
**icon** | a name of the plugin icon. The full list of available icons can be found in the [UI documentation](../ui/icon). Any icon names provided by this font are valid, for example **icon-glass**, **icon-music**. This key is required if `iconSvg` is not set.
**iconSvg** | an SVG icon to be used in place of the standard icon. The SVG icon should be a rectangle and can support colors. This key is required if `icon` is not set.
**homepage** | a link to the author's website address, optional.

<a name="routing-initialization"></a>
## Routing and initialization

Plugin registration files can contain two methods: `boot` and `register`. These methods are run at separate points in the Winter CMS boot process.

The `register` method is called immediately when the plugin is found and the Plugin registration file is read. It can be used to register or provide global services, define functionality within the underlying framework or initialize functionality for the plugin.

```php
public function register()
{
    App::register(MyProviderClass::class, function ($app) {
        return new MyProviderClass();
    });
}
```

> **NOTE:** The `register` method is run very early on in the Winter CMS boot process, and some functions within Winter CMS or Laravel may not be available at that stage, especially in respect to third-party plugins or services. You should use the `boot` method for any functionality that is dependent on third-party plugins or services.

The `boot` method is called after all services are loaded and all plugins are registered. This method should be used to define functionality that is to be run on each page load, such as extending plugins or attaching to events.

```php
public function boot()
{
    User::extend(function($model) {
        $model->hasOne['author'] = ['Acme\Blog\Models\Author'];
    });
}
```

The `boot` and `register` methods are not called during the update process, or within some critical Backend sections and command-line tools, to protect the system from critical errors. To overcome this limitation, use [elevated permissions](#elevated-plugin).

Plugins can also supply a file named **routes.php** that may contain custom routing logic, as defined in the [router service](../services/router). For example:

```php
Route::group(['prefix' => 'api_acme_blog'], function() {
    Route::get('cleanup_posts', function(){ return Posts::cleanUp(); });
});
```

Finally, plugins can also supply a file named **init.php**. This file acts similar to the `boot` method in that it can be used to define functionality run on each page load, but in a more global context.

<a name="dependency-definitions"></a>
## Dependency definitions

A plugin can depend upon other plugins by defining a `$require` property in the [Plugin registration file](#registration-file). The property should contain an array of plugin names that are considered requirements for this plugin to function. A plugin that depends on the **Acme.User** plugin can declare this requirement in the following way:

```php
namespace Acme\Blog;

class Plugin extends \System\Classes\PluginBase
{
    /**
     * @var array Plugin dependencies
     */
    public $require = ['Acme.User'];

    // [...]
}
```

Dependency definitions will affect how the plugin operates and [how the update process orders and applies updates](../plugin/updates#update-process). The installation process will attempt to install any dependencies automatically, however if a plugin is detected in the system without any of its dependencies, it will be automatically disabled to prevent system errors.

Care should be taken to ensure that circular dependency references are not made - for example, if the **Acme.Demo** plugin depends on the **Acme.Blog** plugin, the **Acme.Blog** plugins should not also depend on the **Acme.Demo** plugin.

<a name="extending-twig"></a>
## Extending Twig

Custom Twig filters and functions can be registered in the CMS with the `registerMarkupTags` method of the plugin registration class.

[Twig options](https://twig.symfony.com/doc/2.x/advanced.html#filters) are also able to be passed to change the behavior of the registered filters & functions by providing an array with an `'options'` element containing the options to be passed at time of registration where the callable value would be provided normally. If options are provided, then the callable handler for the filter / function being registered must either be present in a `'callable'` element or as the first element of the array.

>**IMPORTANT:** All custom Twig filters & functions registered via the `MarkupManager` (i.e. `registerMarkupTags()` will have the `is_safe` option set to `['html']` by default, which means that Twig's automatic escaping is disabled by default (effectively it's as if the `| raw` filter was always located after your filter or function's output) unless you provide the `is_safe` option during registration (`'options' => ['is_safe' => []]`).

The next example registers three Twig filters and three functions.

```php
public function registerMarkupTags()
{
    return [
        'filters' => [
            // A global function, i.e str_plural()
            'plural' => 'str_plural',

            // A local method, i.e $this->makeTextAllCaps()
            'uppercase' => [$this, 'makeTextAllCaps']

            // Any callable with custom options defined - first element method
            'userInputToEmojis' => ['input_to_emojis', 'options' => ['is_safe' => []]],
        ],
        'functions' => [
            // A static method call, i.e Form::open()
            'form_open' => ['Winter\Storm\Html\Form', 'open'],

            // Using an inline closure
            'helloWorld' => function() { return 'Hello World!'; }

            // Any callable with custom options defined - named 'callable' method
            'goodbyeWorld' => [
                'callable' => ['Acme\Plugin\Nuke', 'boom'],
                'options'  => ['needs_environment' => true],
            ],
        ],
    ];
}

public function makeTextAllCaps($text)
{
    return strtoupper($text);
}
```

The following Twig custom options are available:

| Option | Type | Default | Description |
| ------ | ---- | ------- | ----------- |
| `needs_environment` | boolean | `false` | if true provides the current `TwigEnvironment` as the first argument to the filter call |
| `needs_context` | boolean | `false` | if true provides the current `TwigContext` as the first argument (second if `needs_environment also set`) to the filter call |
| `is_safe` | array | `[]` | array of languages (usually `html` or `all` are valid values) that the output of the filter / function is safe to be used on without escaping |
| `pre_escape` | string | `''` | (only filters) will pre-escape the value before it is passed to your filter for the language that you set (usually `'html'`) |
| `preserves_safety` | array | `[]` | (only filters) array of languages (usually `html`) that the filter will preserve the safety setting of for previous filters in the chain. i.e. if the previous filter in the chain says that its safe and doesn't require escaping then neither will this one, but if it says that it's unsafe and requires escaping then so will this one. |
| `is_variadic` | boolean | `false` | if true will pass any extra arguments provided to the filter as a single array as the last argument to the filter call |
| `deprecated` | boolean | `false` | if true marks the current filter as being deprecated (usually used with `alternative` to provide an alternative option |
| `alternative` | string | `''` | if `deprecated` is true, provides a recommended alternative filter to use instead. |


<a name="navigation-menus"></a>
## Navigation menus

Plugins can extend the backend navigation menus by overriding the `registerNavigation` method of the [Plugin registration class](#registration-file). This section shows you how to add menu items to the backend navigation area. An example of registering a top-level navigation menu item with two sub-menu items:

```php
public function registerNavigation()
{
    return [
        'blog' => [
            'label'       => 'Blog',
            'url'         => Backend::url('acme/blog/posts'),
            'icon'        => 'icon-pencil',
            'permissions' => ['acme.blog.*'],
            'order'       => 500,
            // Set counter to false to prevent the default behaviour of the main menu counter being a sum of
            // its side menu counters
            'counter'     => ['\Author\Plugin\Classes\MyMenuCounterService', 'getBlogMenuCount'],
            'counterLabel'=> 'Label describing a dynamic menu counter',
            // Optionally you can set a badge value instead of a counter to display a string instead of a numerical counter
            'badge'       => 'New'

            'sideMenu' => [
                'posts' => [
                    'label'       => 'Posts',
                    'icon'        => 'icon-copy',
                    'url'         => Backend::url('acme/blog/posts'),
                    'permissions' => ['acme.blog.access_posts'],
                    'counter'     => 2,
                    'counterLabel'=> 'Label describing a static menu counter',
                ],
                'categories' => [
                    'label'       => 'Categories',
                    'icon'        => 'icon-copy',
                    'url'         => Backend::url('acme/blog/categories'),
                    'permissions' => ['acme.blog.access_categories'],
                ]
            ]
        ]
    ];
}
```

When you register the backend navigation you can use [localization strings](localization) for the `label` values. Backend navigation can also be controlled by the `permissions` values and correspond to defined [backend user permissions](../backend/users). The order in which the backend navigation appears on the overall navigation menu items, is controlled by the `order` value. Higher numbers mean that the item will appear later on in the order of menu items while lower numbers mean that it will appear earlier on.

To make the sub-menu items visible, you may [set the navigation context](../backend/controllers-ajax#navigation-context) in the backend controller using the `BackendMenu::setContext` method. This will make the parent menu item active and display the children in the side menu.

Key | Description
------------- | -------------
**label** | specifies the menu label localization string key, required.
**icon** | an icon name from the [Winter CMS icon collection](../ui/icon), optional.
**iconSvg** | an SVG icon to be used in place of the standard icon, the SVG icon should be a rectangle and can support colors, optional.
**url** | the URL the menu item should point to (ex. `Backend::url('author/plugin/controller/action')`, required.
**counter** | a numeric value to output near the menu icon. The value should be a number or a callable returning a number, optional.
**counterLabel** | a string value to describe the numeric reference in counter, optional.
**badge** | a string value to output in place of the counter, the value should be a string and will override the badge property if set, optional.
**attributes** | an associative array of attributes and values to apply to the menu item, optional.
**permissions** | an array of permissions the backend user must have in order to view the menu item (Note: direct access of URLs still requires separate permission checks), optional.
**code** | a string value that acts as an unique identifier for that menu option. **NOTE**: This is a system generated value and should not be provided when registering the navigation items.
**owner** | a string value that specifies the menu items owner plugin or module in the format "Author.Plugin". **NOTE**: This is a system generated value and should not be provided when registering the navigation items.

<a name="registering-middleware"></a>
## Registering middleware

To register a custom middleware, you can apply it directly to a backend controller in your plugin by using [Controller middleware](../backend/controllers-ajax#controller-middleware), or you can extend a Controller class by using the following method.

```php
public function boot()
{
    \Cms\Classes\CmsController::extend(function($controller) {
        $controller->middleware('Path\To\Custom\Middleware');
    });
}
```

Alternatively, you can push it directly into the Kernel via the following.

```
public function boot()
{
    // Add a new middleware to beginning of the stack.
    $this->app['Illuminate\Contracts\Http\Kernel']
        ->prependMiddleware('Path\To\Custom\Middleware');

    // Add a new middleware to end of the stack.
    $this->app['Illuminate\Contracts\Http\Kernel']
        ->pushMiddleware('Path\To\Custom\Middleware');
}
```

<a name="elevated-plugin"></a>
## Elevated permissions

By default plugins are restricted from accessing certain areas of the system. This is to prevent critical errors that may lock an administrator out from the backend. When these areas are accessed without elevated permissions, the `boot` and `register` [initialization methods](#routing-initialization) for the plugin will not fire.

Request | Description
------------- | -------------
**/combine** | the asset combiner generator URL
**/backend/system/updates** | the site updates context
**/backend/system/install** | the installer path
**/backend/backend/auth** | the backend authentication path (login, logout)
**winter:up** | the CLI command that runs all pending migrations
**winter:update** | the CLI command that triggers the update process
**winter:env** | the CLI command that converts configuration files to environment variables in a `.env` file
**winter:version** | the CLI command that detects the version of Winter CMS that is installed

Define the `$elevated` property to grant elevated permissions for your plugin.

```php
/**
 * @var bool Plugin requires elevated permissions.
 */
public $elevated = true;
```
