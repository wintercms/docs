# Plugin Registration

- [Introduction](#introduction)
    - [Directory structure](#directory-structure)
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
- [Plugin replace](#plugin-replace)
  - [Plugin replace registration](#plugin-replace-registration)
    - [Version constraint](#version-constraints)
  - [Aliases](#aliases)
    - [Config Aliases](#aliases-config)
    - [Lang Aliases](#aliases-lang)
    - [Settings Aliases](#aliases-settings)
    - [Navigation Aliases](#aliases-navigation)
  - [Migrations, seeders and table references](#plugin-migrations)

<a name="introduction"></a>
## Introduction

Plugins are the foundation for adding new features to the CMS by extending it. This article describes the component registration. The registration process allows plugins to declare their features such as [components](components) or back-end menus and pages. Some examples of what a plugin can do:

1. Define [components](components).
1. Define [user permissions](../backend/users).
1. Add [settings pages](settings#backend-pages), [menu items](#navigation-menus), [lists](../backend/lists) and [forms](../backend/forms).
1. Create [database table structures and seed data](updates).
1. Alter [functionality of the core or other plugins](events).
1. Provide classes, [back-end controllers](../backend/controllers-ajax), views, assets, and other files.

<a name="directory-structure"></a>
### Directory structure

Plugins reside in the **/plugins** subdirectory of the application directory. An example of a plugin directory structure:

    plugins/
      acme/              <=== Author name
        blog/            <=== Plugin name
          classes/
          components/
          controllers/
          models/
          updates/
          ...
          Plugin.php     <=== Plugin registration file

Not all plugin directories are required. The only required file is the **Plugin.php** described below. If your plugin provides only a single [component](components), your plugin directory could be much simpler, like this:

    plugins/
      acme/              <=== Author name
        blog/            <=== Plugin name
          components/
          Plugin.php     <=== Plugin registration file

> **Note:** if you are developing a plugin for the [Marketplace](http://wintercms.com/help/site/marketplace), the [updates/version.yaml](updates) file is required.

<a name="namespaces"></a>
### Plugin namespaces

Plugin namespaces are very important, especially if you are going to publish your plugins on the [Winter Marketplace](http://wintercms.com/plugins). When you register as an author on the Marketplace you will be asked for the author code which should be used as a root namespace for all your plugins. You can specify the author code only once, when you register. The default author code offered by the Marketplace consists of the author first and last name: JohnSmith. The code cannot be changed after you register. All your plugin namespaces should be defined under the root namespace, for example `\JohnSmith\Blog`.

<a name="registration-file"></a>
## Registration file

The **Plugin.php** file, called the *Plugin registration file*, is an initialization script that declares a plugin's core functions and information. Registration files can provide the following:

1. Information about the plugin, its name, and author.
1. Registration methods for extending the CMS.

Registration scripts should use the plugin namespace. The registration script should define a class with the name `Plugin` that extends the `\System\Classes\PluginBase` class. The only required method of the plugin registration class is `pluginDetails`. An example Plugin registration file:

    namespace Acme\Blog;

    class Plugin extends \System\Classes\PluginBase
    {
        public function pluginDetails()
        {
            return [
                'name' => 'Blog Plugin',
                'description' => 'Provides some really cool blog features.',
                'author' => 'ACME Corporation',
                'icon' => 'icon-leaf'
            ];
        }

        public function registerComponents()
        {
            return [
                'Acme\Blog\Components\Post' => 'blogPost'
            ];
        }
    }

<a name="registration-methods"></a>
### Supported methods

The following methods are supported in the plugin registration class:

Method | Description
------------- | -------------
**pluginDetails()** | returns information about the plugin.
**register()** | register method, called when the plugin is first registered.
**boot()** | boot method, called right before the request route.
**registerComponents()** | registers any [front-end components](components#component-registration) used by this plugin.
**registerFormWidgets()** | registers any [back-end form widgets](../backend/widgets#form-widget-registration) supplied by this plugin.
**registerListColumnTypes()** | registers any [custom list column types](../backend/lists#custom-column-types) supplied by this plugin.
**registerMailLayouts()** | registers any [mail view layouts](mail#mail-template-registration) supplied by this plugin.
**registerMailPartials()** | registers any [mail view partials](mail#mail-template-registration) supplied by this plugin.
**registerMailTemplates()** | registers any [mail view templates](mail#mail-template-registration) supplied by this plugin.
**registerMarkupTags()** | registers [additional markup tags](#extending-twig) that can be used in the CMS.
**registerNavigation()** | registers [back-end navigation menu items](#navigation-menus) for this plugin.
**registerPermissions()** | registers any [back-end permissions](../backend/users#permission-registration) used by this plugin.
**registerReportWidgets()** | registers any [back-end report widgets](../backend/widgets#report-widget-registration), including the dashboard widgets.
**registerSchedule()** | registers [scheduled tasks](../plugin/scheduling#defining-schedules) that are executed on a regular basis.
**registerSettings()** | registers any [back-end configuration links](settings#link-registration) used by this plugin.
**registerValidationRules()** | registers any [custom validators](../services/validation#custom-validation-rules) supplied by this plugin.

<a name="basic-plugin-information"></a>
### Basic plugin information

The `pluginDetails` is a required method of the plugin registration class. It should return an array containing the following keys:

Key | Description
------------- | -------------
**name** | the plugin name, required.
**description** | the plugin description, required.
**author** | the plugin author name, required.
**icon** | a name of the plugin icon. The full list of available icons can be found in the [UI documentation](../ui/icon). Any icon names provided by this font are valid, for example **icon-glass**, **icon-music**, optional.
**iconSvg** | an SVG icon to be used in place of the standard icon. The SVG icon should be a rectangle and can support colors, optional.
**homepage** | a link to the author's website address, optional.

<a name="routing-initialization"></a>
## Routing and initialization

Plugin registration files can contain two methods `boot` and `register`. With these methods you can do anything you like, like register routes or attach handlers to events.

The `register` method is called immediately when the plugin is registered. The `boot` method is called right before a request is routed. So if your actions rely on another plugin, you should use the boot method. For example, inside the `boot` method you can extend models:

    public function boot()
    {
        User::extend(function($model) {
            $model->hasOne['author'] = ['Acme\Blog\Models\Author'];
        });
    }

> **Note:** The `boot` and `register` methods are not called during the update process to protect the system from critical errors. To overcome this limitation use [elevated permissions](#elevated-plugin).

Plugins can also supply a file named **routes.php** that contain custom routing logic, as defined in the [router service](../services/router). For example:

    Route::group(['prefix' => 'api_acme_blog'], function() {

        Route::get('cleanup_posts', function(){ return Posts::cleanUp(); });

    });

<a name="dependency-definitions"></a>
## Dependency definitions

A plugin can depend upon other plugins by defining a `$require` property in the [Plugin registration file](#registration-file), the property should contain an array of plugin names that are considered requirements. A plugin that depends on the **Acme.User** plugin can declare this requirement in the following way:

    namespace Acme\Blog;

    class Plugin extends \System\Classes\PluginBase
    {
        /**
         * @var array Plugin dependencies
         */
        public $require = ['Acme.User'];

        [...]
    }

Dependency definitions will affect how the plugin operates and [how the update process applies updates](../plugin/updates#update-process). The installation process will attempt to install any dependencies automatically, however if a plugin is detected in the system without any of its dependencies it will be disabled to prevent system errors.

Dependency definitions can be complex but care should be taken to prevent circular references. The dependency graph should always be directed and a circular dependency is considered a design error.

<a name="extending-twig"></a>
## Extending Twig

Custom Twig filters and functions can be registered in the CMS with the `registerMarkupTags` method of the plugin registration class. The next example registers two Twig filters and two functions.

    public function registerMarkupTags()
    {
        return [
            'filters' => [
                // A global function, i.e str_plural()
                'plural' => 'str_plural',

                // A local method, i.e $this->makeTextAllCaps()
                'uppercase' => [$this, 'makeTextAllCaps']
            ],
            'functions' => [
                // A static method call, i.e Form::open()
                'form_open' => ['Winter\Storm\Html\Form', 'open'],

                // Using an inline closure
                'helloWorld' => function() { return 'Hello World!'; }
            ]
        ];
    }

    public function makeTextAllCaps($text)
    {
        return strtoupper($text);
    }

<a name="navigation-menus"></a>
## Navigation menus

Plugins can extend the back-end navigation menus by overriding the `registerNavigation` method of the [Plugin registration class](#registration-file). This section shows you how to add menu items to the back-end navigation area. An example of registering a top-level navigation menu item with two sub-menu items:

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

When you register the back-end navigation you can use [localization strings](localization) for the `label` values. Back-end navigation can also be controlled by the `permissions` values and correspond to defined [back-end user permissions](../backend/users). The order in which the back-end navigation appears on the overall navigation menu items, is controlled by the `order` value. Higher numbers mean that the item will appear later on in the order of menu items while lower numbers mean that it will appear earlier on.

To make the sub-menu items visible, you may [set the navigation context](../backend/controllers-ajax#navigation-context) in the back-end controller using the `BackendMenu::setContext` method. This will make the parent menu item active and display the children in the side menu.

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

To register a custom middleware, you can apply it directly to a Backend controller in your plugin by using [Controller middleware](../backend/controllers-ajax#controller-middleware), or you can extend a Controller class by using the following method.

    public function boot()
    {
        \Cms\Classes\CmsController::extend(function($controller) {
            $controller->middleware('Path\To\Custom\Middleware');
        });
    }

Alternatively, you can push it directly into the Kernel via the following.

    public function boot()
    {
        // Add a new middleware to beginning of the stack.
        $this->app['Illuminate\Contracts\Http\Kernel']
             ->prependMiddleware('Path\To\Custom\Middleware');

        // Add a new middleware to end of the stack.
        $this->app['Illuminate\Contracts\Http\Kernel']
             ->pushMiddleware('Path\To\Custom\Middleware');
    }

<a name="elevated-plugin"></a>
## Elevated permissions

By default plugins are restricted from accessing certain areas of the system. This is to prevent critical errors that may lock an administrator out from the back-end. When these areas are accessed without elevated permissions, the `boot` and `register` [initialization methods](#routing-initialization) for the plugin will not fire.

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

    /**
     * @var bool Plugin requires elevated permissions.
     */
    public $elevated = true;

<a name="plugin-replace"></a>
## Replacing plugins

Plugin replacement is a feature that allows you to create a plugin that replaces (or overrides) another plugin.
This can be useful when you're forking a plugin to add your own functionality but want to keep the plugin as a
requirement of other plugins.

<a name="plugin-replace-registration"></a>
### Plugin replace registration

To enable plugin replacement, specify the original plugin you are replacing in your plugin details along with your
version constraint.

    public function pluginDetails()
    {
        return [
            'name'        => 'Acme Plugin',
            'replaces'    => [
                'Acme.Original' => '>=5.0 <=6.0.4'
            ]
        ];
    }

<a name="version-constraints"></a>
#### Version constraints

Version constraints allow you to restrict your plugin to only override currently installed plugins of
specific versions. For example, this would allow you to only replace a plugin upto that plugins version `2.0.0`.

This means you don't have to worry about new versions of the original plugin having changes that may conflict with
your changes to the plugin.

Version constraints are specified in the same manner as composer. For instance some valid examples would be:

- `1.0`
- `>=1.0.3`
- `<2.0`
- `>=1.5.0 <2.0.0`
- `self.version`

By specifying a version, your plugin will check what version the original plugin is installed at and only if
it's version matches the constraint will it disable the original and enable the replacement. If this match fails
then the replacement will be disabled and the original plugin will stay enabled.

<a name="aliases"></a>
### Aliases

<!-- TODO: Group the into their own docs -->

Aliasing is a feature of Winter that allows for backwards compatibility and support for inheriting replaced plugins:

- Configs
- Lang
- Settings
- Navigation

<a name="aliases-config"></a>
#### Config

Config supports 2 different types of aliasing: `registerNamespaceAlias` & `registerPackageFallback`.

##### registerNamespaceAlias

This method allows for redirection of the alias to the namespace while accessing config values.

    Config::registerNamespaceAlias(/* string $namespace */ 'winter.namespace', /* string $alias */ 'winter.alias');

For example, register the following config as `plugins/winter/namespace/config/config.php`:

    <?php
    
    return [
        'foo' => 'bar'
    ];

The config will be accessible via the alias registered:

    config('winter.alias::foo'); // returns bar

##### registerPackageFallback

This method allows falling back to an aliased global config (a config specified in `/config/acme/plugin/config.php`).

    Config::registerPackageFallback(/* string $namespace */ 'winter.namespace', /* string $alias */ 'winter.alias');

The logic to this is as follows:

- If `/config/winter/namespace/config.php` exists it will be registered under the `winter.namespace` namespace.
- If `/config/winter/namespace/config.php` does not exist, it will check `/config/winter/alias/config.php` and if found,
  it will be registered under the `winter.namespace`.

<a name="aliases-lang"></a>
#### Lang

Allows for redirection of calls to the alias and returns values from the namespace.

    Lang::registerNamespaceAlias(/* string $namespace */ 'winter.namespace', /* string $alias */ 'winter.alias');

For example, register the following config as `plugins/winter/namespace/lang/en/lang.php`:

    <?php
    
    return [
        'foo' => 'bar'
    ];

The lang will be accessible via the alias registered:

    Lang::get('winter.alias::foo'); // returns bar

<a name="aliases-settings"></a>
#### Settings

There are 2 methods for registering settings aliases. Firstly the aliases can be registered prior to the `PluginManager`
init via `lazyRegisterOwnerAlias`.

    SettingsManager::lazyRegisterOwnerAlias(string $namespace, string $alias);

If the `PluginManager` has been loaded, then aliases can be registered via:

    SettingsManager::instance()->registerOwnerAlias(string $namespace, string $alias);

<a name="aliases-navigation"></a>
#### Navigation

There are 2 methods for registering settings aliases. Firstly the aliases can be registered prior to the `PluginManager`
init via `lazyRegisterOwnerAlias`.

    NavigationManager::lazyRegisterOwnerAlias(string $namespace, string $alias);

If the `PluginManager` has been loaded, then aliases can be registered via:

    NavigationManager::instance()->registerOwnerAlias(string $namespace, string $alias);

<a name="plugin-migrations"></a>
### Migrations, seeders and table references

When forking a plugin and using the replace functionality, you will need to manage migrations, seeders and models. To
do this we recommend the following:

- Create a migration to rename tables
- Update models to reference your new table
- Check migrations for any usage of models

#### Table renaming

An example migration could look something like this:

    const TABLES = [
        'example',
        'foo',
        'bar'
    ];

    public function up()
    {
        foreach (self::TABLES as $table) {
            $from = 'acme_plugin_' . $table;
            $to = 'winter_plugin_' . $table;

            if (Schema::hasTable($from) && !Schema::hasTable($to)) {
                Schema::rename($from, $to);
            }
        }
    }

    public function down()
    {
        foreach (self::TABLES as $table) {
            $from = 'winter_plugin_' . $table;
            $to = 'acme_plugin_' . $table;

            if (Schema::hasTable($from) && !Schema::hasTable($to)) {
                Schema::rename($from, $to);
            }
        }
    }

#### Migrations using models

If a migration is using a model to populate data, it will be referencing the new table and that will cause issues 
while updating. The solution to this is dynamically renaming the table before inserting/modifying data:

    ExampleModel::extend(function ($model) {
        $model->setTable('acme_plugin_example');
    });

    // execute seeding code

    ExampleModel::extend(function ($model) {
        $model->setTable('winter_plugin_example');
    });
