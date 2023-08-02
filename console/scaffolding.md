# Scaffolding Commands

The following commands allow you to quickly scaffold additional code into your Winter project, speeding up development time.

## Create a theme

```bash
php artisan create:theme <theme code>
```

The `create:theme` command generates a theme folder and basic files for the theme. The first argument specifies the theme code, eg. `myauthor-mytheme`.

## Create a plugin

```bash
php artisan create:plugin <plugin code>
```

The `create:plugin` command generates a plugin folder and basic files for the plugin. The first argument specifies the author and plugin name, eg. `MyAuthor.MyPlugin`.

## Create a component

```bash
php artisan create:component <plugin code> <component name>
```

The `create:component` command creates a new component class and the default component view. The first argument specifies the plugin code of the plugin that this component will be added into, and the second parameter specifies the component class name, eg. `MyComponent`.

## Create a model

```bash
php artisan create:model <plugin code> <model name>
```

The `create:model` command generates the files needed for a new model. The first argument specifies the plugin code of the plugin that this model will be added into, and the second parameter specifies the model class name, eg. `MyModel`.

## Create a settings model

```bash
php artisan create:settings <plugin code> [model name]
```

The `create:settings` command generates the files needed for a new [Settings model](../plugin/settings#database-settings). The first argument specifies the plugin code of the plugin that this model will be added into, and the second parameter is optional and specifies the Settings model class name (defaults to `Settings`).

## Create a backend controller

```bash
php artisan create:controller <plugin code> <controller name>
```

The `create:controller` command generates a controller, configuration and view files. The first argument specifies the plugin code of the plugin that this controller will be added into, and the second parameter specifies the controller class name, eg. `MyController`.

## Create a form widget

```bash
php artisan create:formwidget <plugin code> <widget name>
```

The `create:formwidget` command generates a backend form widget, view and basic asset files. The first argument specifies the plugin code of the plugin that this form widget will be added into, and the second parameter specifies the form widget class name, eg. `MyFormWidget`.

## Create a report widget

```bash
php artisan create:reportwidget <plugin code> <widget name>
```

The `create:reportwidget` command generates a backend report widget, view and basic asset files. The first argument specifies the plugin code of the plugin that this report widget will be added into, and the second parameter specifies the report widget class name, eg. `MyReportWidget`.

## Create a job

The `create:job` command generates a job. The first argument specifies the plugin code of the plugin that this job will be added into, and the second parameter specifies the job class name, eg. `MyJob`.

```bash
php artisan create:job <plugin code> <job name>
```

## Create a console command

```bash
php artisan create:command <plugin code> <command name>
```

The `create:command` command generates a [new console command](../console/introduction#building-a-command). The first argument specifies the plugin code of the plugin that this console command will be added into, and the second parameter specifies the command name.
