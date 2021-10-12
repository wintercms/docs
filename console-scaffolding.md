# Scaffolding Commands

- [Scaffolding commands](#scaffolding-commands)
    - [Create a theme](#scaffold-create-theme)
    - [Create a plugin](#scaffold-create-plugin)
    - [Create a component](#scaffold-create-component)
    - [Create a model](#scaffold-create-model)
    - [Create a settings model](#scaffold-create-settings-model)
    - [Create a backend controller](#scaffold-create-controller)
    - [Create a form widget](#scaffold-create-formwidget)
    - [Create a report widget](#scaffold-create-reportwidget)
    - [Create a console command](#scaffold-create-command)

<a name="scaffolding-commands"></a>
## Scaffolding commands

Use the scaffolding commands to speed up the development process.

<a name="scaffold-create-theme"></a>
### Create a Theme

The `create:theme` command generates a theme folder and basic files for the theme. The parameter specifies the theme code.

    php artisan create:theme myauthor-mytheme

<a name="scaffold-create-plugin"></a>
### Create a Plugin

The `create:plugin` command generates a plugin folder and basic files for the plugin. The parameter specifies the author and plugin name.

    php artisan create:plugin Acme.Blog

<a name="scaffold-create-component"></a>
### Create a Component

The `create:component` command creates a new component class and the default component view. The first parameter specifies the author and plugin name. The second parameter specifies the component class name.

    php artisan create:component Acme.Blog Post

<a name="scaffold-create-model"></a>
### Create a Model

The `create:model` command generates the files needed for a new model. The first parameter specifies the author and plugin name. The second parameter specifies the model class name.

    php artisan create:model Acme.Blog Post

<a name="scaffold-create-settings-model"></a>
### Create a Settings Model

The `create:settings` command generates the files needed for a new [Settings model](../plugin/settings#database-settings). The first parameter specifies the author and plugin name. The second parameter is optional and specifies the Settings model class name (defaults to `Settings`).

    php artisan create:settings Acme.Blog CustomSettings

<a name="scaffold-create-controller"></a>
### Create a backend Controller

The `create:controller` command generates a controller, configuration and view files. The first parameter specifies the author and plugin name. The second parameter specifies the controller class name.

    php artisan create:controller Acme.Blog Posts

<a name="scaffold-create-formwidget"></a>
### Create a FormWidget

The `create:formwidget` command generates a backend form widget, view and basic asset files. The first parameter specifies the author and plugin name. The second parameter specifies the form widget class name.

    php artisan create:formwidget Acme.Blog CategorySelector

<a name="scaffold-create-reportwidget"></a>
### Create a ReportWidget

The `create:reportwidget` command generates a backend report widget, view and basic asset files. The first parameter specifies the author and plugin name. The second parameter specifies the report widget class name.

    php artisan create:reportwidget Acme.Blog TopPosts

<a name="scaffold-create-command"></a>
### Create a console Command

The `create:command` command generates a [new console command](../console/development). The first parameter specifies the author and plugin name. The second parameter specifies the command name.

    php artisan create:command Winter.Blog MyCommand
