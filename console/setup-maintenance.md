# Setup & Maintenance Commands

The following commands are used for the setup and maintenance of a Winter installation.

## Install Winter via command line

```bash
php artisan winter:install
```

The `winter:install` command will guide you through the process of setting up Winter CMS for the first time. It will ask for the database configuration, application URL, encryption key and administrator details.

You also may wish to inspect **config/app.php** and **config/cms.php** to change any additional configuration.

> **NOTE:** You cannot run `winter:install` after running [`winter:env`](#configure-winter-through-an-environment-file). The `winter:env` command takes the existing configuration values and puts them in the `.env` file while replacing the original values with calls to `env()` within the configuration files. `winter:install` cannot replace those calls to `env()` within the configuration files as that would be overly complex to manage.

## Update Winter and its plugins

```bash
php artisan winter:update
```

The `winter:update` command will request updates from the Winter gateway. It will update the core application and plugin files, then perform a database migration.

> **IMPORTANT**: If you are using [using Composer](../architecture/using-composer), do **NOT** run this command without first making sure that `cms.disableCoreUpdates` is set to `true`. Doing so will cause conflicts between the marketplace version of Winter and the version available through Composer. In order to update the core Winter installation when using Composer, run `composer update` instead.

## Run database migrations

The `winter:up` command will perform a database migration, creating database tables and executing seed scripts, provided by the system and [plugin version history](../plugin/updates). The migration command can be run multiple times - it will only execute a migration or seed script once, which means only new changes are applied.

```bash
php artisan winter:up
```

The inverse command `winter:down` will reverse all migrations, dropping database tables and deleting data. Care should be taken when using this command. The [plugin refresh command](../console/plugin-management#refresh-a-plugin) is a useful alternative for debugging a single plugin.

```bash
php artisan winter:down
```

## Change an administrator's password

```bash
php artisan winter:passwd [username] [password]
```

The `winter:passwd` command will allow the password of a backend user or administrator to be changed via the command-line. This is useful if someone gets locked out of their Winter CMS install, or for changing the password for the default administrator account.

You may provide the username/email and password as both the first and second argument, or you may leave the arguments blank, in which case the command will be run interactively.

## Configure Winter through an environment file

```bash
php artisan winter:env
```

The `winter:env` command allows you to convert the configuration of Winter to use an environment variable file. The command will create a `.env` file in the root folder of your project, and change certain configuration variables in the `config` folder to use these environment variables instead.

This setup is recommend if you use automated deployment tools, and provides a level of security by removing passwords or sensitive information from your configuration files (which are normally stored in source control) and places them in the environment file, which you should not include in source control.

You are not restricted from providing environment variables through another method, for example, you may store the environment variables in your server's environment, or provide them through the PHP configuration.

## Get the installed Winter version

```bash
php artisan winter:version [--changes] [--only-version]
```

The `winter:version` command displays the installed version of Winter. This is determined by querying a [central build manifest](https://github.com/wintercms/meta/blob/master/manifest/builds.json) and verifying the integrity of each system file in Winter against each build in this manifest. This allows the command to determine if any modifications have been made to the system files.

If modifications are detected, this command will try and best-guess which version is installed, but will alert you that modifications have been made.

If you wish to review the files that have been modified, you can add the `--changes` flag to be provided with a list of files that have been added, modified or removed from Winter.

If you wish to retrieve just the build version - for example, for automated scripts - you may use the `-o` or `--only-version` option. Note that this will suppress all other output, including the missing database warning or changes list if you are also using the `--changes` flag.

## Remove the demo plugin and theme

```bash
php artisan winter:fresh
```

The `winter:fresh` command will remove the demo plugin and theme that is included with every Winter installation, if these are still found in your installation.

## Mirror public files

```bash
php artisan winter:mirror public [--relative]
```

The `winter:mirror` command creates a mirrored copy of the public files needed to serve the application, using symbolic linking. This command is used when [setting up a public folder](../setup/configuration#using-a-public-folder) and is recommended for security purposes as it prevents direct access to system files.

This command should be re-run whenever plugins and themes are installed or removed.

By default, this command will create absolute symlink paths. If you wish to use relative paths instead, you may add the `--relative` flag to do so.
