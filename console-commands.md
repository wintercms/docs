# Command Line Interface

- [Console installation](#console-install)
    - [Quick start install](#console-install-quick)
    - [Composer install](#console-install-composer)
- [Setup & Maintenance](#maintenance-commands)
    - [Install command](#console-install-command)
    - [System update](#console-update-command)
    - [Database migration](#console-up-command)
    - [Change backend user password](#change-backend-user-password-command)
- [Plugin management](#plugin-commands)
    - [Install plugin](#plugin-install-command)
    - [Refresh plugin](#plugin-refresh-command)
    - [Rollback plugin](#plugin-rollback-command)
    - [List plugin](#plugin-list-command)
    - [Disable plugin](#plugin-disable-command)
    - [Enable plugin](#plugin-enable-command)
    - [Remove plugin](#plugin-remove-command)
- [Theme management](#theme-commands)
    - [Install theme](#theme-install-command)
    - [List themes](#theme-list-command)
    - [Enable theme](#theme-use-command)
    - [Remove theme](#theme-remove-command)
    - [Sync theme](#theme-sync-command)
- [Utilities](#utility-commands)
    - [Clear Application Cache](#cache-clear-command)
    - [Remove Demo Data](#winter-fresh-command)
    - [Mirror Public Directory](#cache-clear-command)
    - [Enable DotEnv Configuration](#winter-env-command)
    - [Run Unit Tests](#winter-test-command)
    - [Miscellaneous Commands](#winter-util-command)
        - [Compile Asset Bundles](#winter-util-compile-assets-command)
        - [Recursively Git Pull](#winter-util-git-pull-command)
        - [Purge Thumbnails](#winter-util-purge-thumbs-command)
        - [Purge Uploaded Files](#winter-util-purge-uploads-command)
        - [Purge Orphaned Files](#winter-util-purge-orphans-command)

Winter includes several command-line interface (CLI) commands and utilities that allow to install Winter, update it, as well as speed up the development process. The console commands are based on Laravel's [Artisan](http://laravel.com/docs/artisan) tool. You may [develop your own console commands](../console/development) or speed up development with the provided [scaffolding commands](../console/scaffolding).

<a name="console-install"></a>
## Console installation

Console installation can be performed using the native system or with [Composer](http://getcomposer.org/) to manage dependencies. Either approach will download the Winter application files and can be used right away. If you plan on using a database, be sure to run the [install command](#console-install-command) after installation.

<a name="console-install-composer"></a>
### Composer install

Download the application source code by using `create-project` in your terminal. The following command will install to a directory called **/mywinter**.

```bash
composer create-project wintercms/winter mywinter
```

Once this task has finished, open the file **config/cms.php** and enable the `disableCoreUpdates` setting. This will disable core updates from being delivered by the Winter gateway.

```php
return [
    // ...

    'disableCoreUpdates' => true,

    // ...
];
```

When updating Winter, use the composer update command as normal before performing a [database migration](#console-up-command).

```bash
composer update
```

Composer is configured to look inside plugin directories for composer dependencies through the use of the `wikimedia-merge-plugin` and these will be included when running `composer update`.

> **NOTE:** To use composer with a Winter instance that has been installed using the [Wizard installation](../setup/installation#wizard-installation), simply copy the `tests/` directory and `composer.json` file from [GitHub](https://github.com/wintercms/winter) into your Winter instance and then run `composer install`.

<a name="maintenance-commands"></a>
## Setup & Maintenance

<a name="console-install-command"></a>
### Install command

The `winter:install` command will guide you through the process of setting up Winter CMS for the first time. It will ask for the database configuration, application URL, encryption key and administrator details.

```bash
php artisan winter:install
```

You also may wish to inspect **config/app.php** and **config/cms.php** to change any additional configuration.

> **NOTE:** You cannot run `winter:install` after running `winter:env`. `winter:env` takes the existing configuration values and puts them in the `.env` file while replacing the original values with calls to `env()` within the configuration files. `winter:install` cannot now replace those calls to `env()` within the configuration files as that would be overly complex to manage.

<a name="console-update-command"></a>
### System update

The `winter:update` command will request updates from the Winter gateway. It will update the core application and plugin files, then perform a database migration.

```bash
php artisan winter:update
```

> **IMPORTANT**: If you are using [using composer](#console-install-composer) do **NOT** run this command without first making sure that `cms.disableCoreUpdates` is set to true. Doing so will cause conflicts between the marketplace version of Winter and the version available through composer. In order to update the core Winter installation when using composer run `composer update` instead.

<a name="console-up-command"></a>
### Database migration

The `winter:up` command will perform a database migration, creating database tables and executing seed scripts, provided by the system and [plugin version history](../plugin/updates). The migration command can be run multiple times, it will only execute a migration or seed script once, which means only new changes are applied.

```bash
php artisan winter:up
```

The inverse command `winter:down` will reverse all migrations, dropping database tables and deleting data. Care should be taken when using this command. The [plugin refresh command](#plugin-refresh-command) is a useful alternative for debugging a single plugin.

```bash
php artisan winter:down
```

<a name="change-backend-user-password-command"></a>
### Change backend user password

The `winter:passwd` command will allow the password of a backend user or administrator to be changed via the command-line. This is useful if someone gets locked out of their Winter CMS install, or for changing the password for the default administrator account.

```bash
php artisan winter:passwd username password
```

You may provide the username/email and password as both the first and second argument, or you may leave the arguments blank, in which case the command will be run interactively.

<a name="plugin-commands"></a>
## Plugin management

Winter includes a number of commands for managing plugins.

<a name="plugin-install-command"></a>
### Install plugin

`plugin:install` - downloads and installs the plugin by its name. The next example will install a plugin called **AuthorName.PluginName**. Note that your installation should be bound to a project in order to use this command. You can create projects on Winter website, in the [Account / Projects](https://wintercms.com/account/project/dashboard) section.

```bash
php artisan plugin:install AuthorName.PluginName
```

> **NOTE:** If you have already have the plugin files locally either through Composer or manually uploading them then you can just run [`winter:up`](#console-up-command) to run the plugin's pending migrations to "install" it. This command is mostly meant for instaling plugins sourced from the [Winter CMS Marketplace](https://wintercms.com/marketplace)

<a name="plugin-refresh-command"></a>
### Refresh plugin

`plugin:refresh` - destroys the plugin's database tables and recreates them. This command is useful for development.

```bash
php artisan plugin:refresh AuthorName.PluginName
```

<a name="plugin-rollback-command"></a>
### Rollback plugin

`plugin:rollback` - Rollback the specified plugin's migrations. The second parameter is optional, if specified the rollback process will stop at the specified version.

```bash
php artisan plugin:rollback AuthorName.PluginName 1.2.3
```

<a name="plugin-list-command"></a>
### List Plugins

`plugin:list` - Displays a list of installed plugins.

```bash
php artisan plugin:list
```

<a name="plugin-disable-command"></a>
### Disable Plugin

`plugin:disable` - Disable an existing plugin.

```bash
php artisan plugin:disable AuthorName.PluginName
```

<a name="plugin-enable-command"></a>
### Enable Plugin

`plugin:enable` - Enable a disabled plugin.

```bash
php artisan plugin:enable AuthorName.PluginName
```

<a name="plugin-remove-command"></a>
### Remove plugin

`plugin:remove` - destroys the plugin's database tables and deletes the plugin files from the filesystem.

```bash
php artisan plugin:remove AuthorName.PluginName
```

<a name="theme-commands"></a>
## Theme management

Winter includes a number of commands for managing themes.

<a name="theme-install-command"></a>
### Install theme

`theme:install` - download and install a theme from the [Marketplace](https://wintercms.com/marketplace). The following example will install the theme in `/themes/authorname-themename`

```bash
php artisan theme:install AuthorName.ThemeName
```

If you wish to install the theme in a custom directory, simply provide the second argument. The following example will download `AuthorName.ThemeName` and install it in `/themes/my-theme`

```bash
php artisan theme:install AuthorName.ThemeName my-theme
```

<a name="theme-list-command"></a>
### List themes

`theme:list` - list installed themes. Use the **-m** option to include popular themes in the Marketplace.

```bash
php artisan theme:list
```

<a name="theme-use-command"></a>
### Enable theme

`theme:use` - switch the active theme. The following example will switch to the theme in `/themes/winter-vanilla`

```bash
php artisan theme:use winter-vanilla
```

<a name="theme-remove-command"></a>
### Remove theme

`theme:remove` - delete a theme. The following example will delete the directory `/themes/winter-vanilla`

```bash
php artisan theme:remove winter-vanilla
```

<a name="theme-sync-command"></a>
### Sync theme

`theme:sync` - Sync a theme's content between the filesystem and database when `cms.databaseTemplates` is enabled.

```bash
php artisan theme:sync
```

By default the theme that will be synced is the currently active one. You can specify any theme to sync by passing the desired theme's code:

```bash
php artisan theme:sync my-custom-theme
```

By default the sync direction will be from the database to the filesytem (i.e. you're syncing changes on a remote host to the filesystem for tracking in a version control system). However, you can change the direction of the sync by specifying `--target=database`. This is useful if you have changed the underlying files that make up the theme and you want to force the site to pick up your changes even if they have made changes of their own that are stored in the database.

```bash
php artisan theme:sync --target=database
```

By default the command requires user interaction to confirm that they want to complete the sync (including information about the amount of paths affected, the theme targeted, and the target & source of the sync). To override the need for user interaction (i.e. if running this command in a deploy / build script of some sort) just pass the `--force` option:

```bash
php artisan theme:sync --force
```

Unless otherwise specified, the command will sync all the valid paths (determined by the Halcyon model instances returned to the `system.console.theme.sync.getAvailableModelClasses` event) available in the theme. To manually specify specific paths to be synced pass a comma separated list of paths to the `--paths` option:

```bash
php artisan theme:sync --paths=partials/header.htm,content/contact.md
```

<a name="utility-commands"></a>
## Utilities

Winter includes a number of utility commands.

<a name="cache-clear-command"></a>
### Clear Application Cache

`cache:clear` - clears the application, twig and combiner cache directories. Example:

```bash
php artisan cache:clear
```

<a name="winter-fresh-command"></a>
### Remove Demo Data

`winter:fresh` - removes the demo theme and plugin that ships with Winter.

```bash
php artisan winter:fresh
```

<a name="cache-clear-command"></a>
### Mirror Public Directory

`winter:mirror` - creates a mirrored copy of the public files needed to serve the application, using symbolic linking. This command is used when [setting up a public folder](../setup/configuration#public-folder).

```bash
php artisan winter:mirror public
```

> **NOTE:** By default the symlinks created will be absolute symlinks, to create them as relative symlinks instead include the `--relative` option:

```bash
php artisan winter:mirror public --relative
```

<a name="winter-env-command"></a>
### Enable DotEnv Configuration

`winter:env` - changes common configuration values to [DotEnv syntax](../setup/configuration#dotenv-configuration).

    php artisan winter:env

<a name="winter-test-command"></a>
### Run unit tests

Runs the unit tests for the entire project, a specific plugin, or the Winter CMS core.

To run the entire project's unit tests:

```bash
php artisan winter:test
```

Or, to run only the core unit tests, use the `-o` or `--core` option:

```bash
php artisan winter:test -o
```

To run a specific plugin's tests, use the `-p` or `--plugin=` option:

```bash
php artisan winter:test -p Acme.Demo
```

To run a custom test suite, use the `-c` or `--configuration=` option:

```bash
php artisan winter:test -c ./custom-path/phpunit.xml
```

If using additional PHPUnit parameters / options, they must be included after the winter:test command's options:

```bash
php artisan winter:test -p Acme.Demo --filter=FilteredTest --stop-on-failure
```

<a name="winter-util-command"></a>
### Miscellaneous commands

`winter:util` - a generic command to perform general utility tasks, such as cleaning up files or combining files. The arguments passed to this command will determine the task used.

<a name="winter-util-compile-assets-command"></a>
#### Compile Asset Bundles

Outputs combined system files for JavaScript (js), StyleSheets (less), client side language (lang), or everything (assets).

```bash
php artisan winter:util compile assets
php artisan winter:util compile lang
php artisan winter:util compile js
php artisan winter:util compile less
```

To combine without minification, pass the `--debug` option.

```bash
php artisan winter:util compile js --debug
```

<a name="winter-util-git-pull-command"></a>
#### Pull all repos

This will execute the command `git pull` on all theme and plugin directories.

```bash
php artisan winter:util git pull
```

<a name="winter-util-purge-thumbs-command"></a>
#### Purge Thumbnails

Deletes all generated thumbnails in the uploads directory.

```bash
php artisan winter:util purge thumbs
```

<a name="winter-util-purge-uploads-command"></a>
#### Purge Uploaded Files

Deletes files in the uploads directory that do not exist in the "system_files" table.

```bash
php artisan winter:util purge uploads
```

<a name="winter-util-purge-orphans-command"></a>
#### Purge Orphaned Files

Deletes records in "system_files" table that do not belong to any other model.

```bash
php artisan winter:util purge orphans
```

To also delete records that have no associated file in the local storage, pass the `--missing-files` option.

```bash
php artisan winter:util purge orphans --missing-files
```
