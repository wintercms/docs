# Scaffolding Commands

- [Create a theme](#create-theme)
- [Create a plugin](#create-plugin)
- [Create a component](#create-component)
- [Create a model](#create-model)
- [Create a settings model](#create-settings-model)
- [Create a backend controller](#create-controller)
- [Create a form widget](#create-formwidget)
- [Create a report widget](#create-reportwidget)
- [Create a console command](#create-command)

The following commands allow you to quickly scaffold additional code into your Winter project, speeding up development time.

<a name="create-theme"></a>
## Create a theme

```bash
php artisan create:theme <theme code>
```

The `create:theme` command generates a theme folder and basic files for the theme. The first argument specifies the theme code, eg. `myauthor-mytheme`.

<a name="create-plugin"></a>
## Create a plugin

```bash
php artisan create:plugin <plugin code>
```

The `create:plugin` command generates a plugin folder and basic files for the plugin. The first argument specifies the author and plugin name, eg. `MyAuthor.MyPlugin`.

<a name="create-component"></a>
## Create a component

```bash
php artisan create:component <plugin code> <component name>
```

The `create:component` command creates a new component class and the default component view. The first argument specifies the plugin code of the plugin that this component will be added into, and the second parameter specifies the component class name, eg. `MyComponent`.

<a name="create-model"></a>
## Create a model

```bash
php artisan create:model <plugin code> <model name>
```

The `create:model` command generates the files needed for a new model. The first argument specifies the plugin code of the plugin that this model will be added into, and the second parameter specifies the model class name, eg. `MyModel`.

<a name="create-settings-model"></a>
## Create a settings model

```bash
php artisan create:settings <plugin code> [model name]
```

The `create:settings` command generates the files needed for a new [Settings model](../plugin/settings#database-settings). The first argument specifies the plugin code of the plugin that this model will be added into, and the second parameter is optional and specifies the Settings model class name (defaults to `Settings`).

<a name="create-controller"></a>
## Create a backend controller

```bash
php artisan create:controller <plugin code> <controller name>
```

The `create:controller` command generates a controller, configuration and view files. The first argument specifies the plugin code of the plugin that this controller will be added into, and the second parameter specifies the controller class name, eg. `MyController`.

<a name="create-formwidget"></a>
## Create a form widget

```bash
php artisan create:formwidget <plugin code> <widget name>
```

The `create:formwidget` command generates a backend form widget, view and basic asset files. The first argument specifies the plugin code of the plugin that this form widget will be added into, and the second parameter specifies the form widget class name, eg. `MyFormWidget`.

<a name="create-reportwidget"></a>
## Create a report widget

```bash
php artisan create:reportwidget <plugin code> <widget name>
```

The `create:reportwidget` command generates a backend report widget, view and basic asset files. The first argument specifies the plugin code of the plugin that this report widget will be added into, and the second parameter specifies the report widget class name, eg. `MyReportWidget`.

<a name="create-command"></a>
## Create a console command

```bash
php artisan create:command <plugin code> <command name>
```

The `create:command` command generates a [new console command](../console/development). The first argument specifies the plugin code of the plugin that this console command will be added into, and the second parameter specifies the command name.
