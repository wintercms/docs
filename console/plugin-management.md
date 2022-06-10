# Plugin Management Commands

The following commands are used for managing plugins within your Winter installation.

## Download and install a plugin for Winter

```bash
php artisan plugin:install <plugin code>
```

The `plugin:install` command downloads and installs the plugin by its plugin code in the format **AuthorName.PluginName**. You can retrieve the plugin code through the Winter marketplace.

Note that your installation should be bound to a project in order to use this command. You can create projects on the Winter website, in the [Account / Projects](https://wintercms.com/account/project/dashboard) section.

> **NOTE:** If you have already have the plugin files locally either through Composer or manually uploading them then you can just run [`winter:up`](#console-up-command) to run the plugin's pending migrations to "install" it. This command is mostly meant for instaling plugins sourced from the [Winter CMS Marketplace](https://wintercms.com/marketplace).

## List installed plugins

```bash
php artisan plugin:list
```

The `plugin:list` command will generate a table of installed plugins in your Winter installation, including the version installed, whether the plugin is enabled or disabled and if updates are frozen for the plugin or not.

Each plugin is listed by its plugin code, allowing you to use the code for other plugin commands listed here.

## Refresh a plugin

```bash
php artisan plugin:refresh <plugin code>
```

The `plugin:refresh` command allows you to rollback a plugin, destroying its database records and tables, and re-run all updates on the plugin. **This is a destructive action.** You will be prompted to confirm the action before proceeding.

This command is made available mainly for plugin development.

## Rollback a plugin

```bash
php artisan plugin:rollback <plugin code> [version]
```

The `plugin:rollback` command allows you to rollback a plugin, optionally to a specified version. It can be useful for rolling back a plugin which has introduced an error in your Winter installation, rolling it back to a version that worked previously. **This is a destructive action.** You will be prompted to confirm the action before proceeding.

The `version` argument is optional - if it is not specified, the plugin is rolled back completely.

## Enable a plugin

```bash
php artisan plugin:enable <plugin code>
```

The `plugin:enable` command allows you to enable a previously disabled plugin. The plugin will be able to function in your Winter installation once more.

## Disable a plugin

```bash
php artisan plugin:disable <plugin code>
```

The `plugin:disable` command allows you to disable a previously enabled plugin. The plugin will no longer function in your Winter installation. If the plugin disabled is a requirement of another plugin installed, that plugin will also be disabled.

## Remove a plugin

```bash
php artisan plugin:remove <plugin code>
```

The `plugin:remove` command allows you to remove a plugin installed on your Winter CMS installation. This will remove both the files for the plugin, and the database records and tables. **This is a destructive action.** You will be prompted to confirm the action before proceeding.
