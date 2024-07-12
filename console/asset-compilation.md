# Asset Compilation

## Introduction

Winter brings first-class support for handling Node-based compilation for frontend assets through the Mix commands. The comamnds use the [Laravel Mix](https://laravel-mix.com/) wrapper, a user-friendly and simple interface for setting up compilation of multiple types of frontend assets through Webpack and various libraries.

### Requirements

To take advantage of Mix asset compilation, you must have Node and the Node package manager (NPM) installed in your development environment. This will be dependent on your operating system - please review the [Download NodeJS](https://nodejs.org/en/download/) page for more information on installing Node.

[Laravel Mix](https://laravel-mix.com/) should also be present in the `package.json` file for any packages that will be using it (either in `dependencies` or a `devDependencies`) but if it is not specified in the project's `package.json` file then it can be optionally automatically added when running the [`mix:install`](#install-node-dependencies) command.

## Registering a package

Registering for asset compilation through Mix is very easy. Automatic registration should meet your needs most of the time, and if not there are several methods available to manually register Mix packages.

### Automatic registration

By default, Winter will scan all available and enabled modules, plugins and themes for the presence of a config file under each extension's root folder (i.e. `modules/system/config_file.js`, `plugins/myauthor/myplugin/config_file.js`, or `themes/mytheme/config_file.js`).

If the config file is found, it will be automatically registered as a package with an automatically generated package name, and will show up when running the Mix commands. Most of the time, this should be all you need to do in order to get started with one of the asset compilers in Winter CMS.

The currently supported config files are:

- `winter.mix.js` - for the Mix compiler.
- `vite.config.js` - for the Vite compiler.

### Supported Asset Compilers

Winter CMS supports the following:

- [Laravel Mix](./asset-compilation-mix.md).
- [Vite (with Laravel HRM installed)](./asset-compilation-vite.md).

For more information on these compilers, see the links above.

### Run a package script

```bash
php artisan npm:run <package> <script> [-f|--production] [-- <extra script args>]
```

The `npm:run` command allows you to quickly run scripts defined in the `package.json` file of a Mix package.

```json5
// package.json
{
    // ...
    "scripts": {
        "scriptName": "script to execute"
    }
    // ...
}
```

This can be useful for running arbitrary scripts that augment the capabilities of your plugin or theme, such as executing unit tests, making customised builds and much more. Note that scripts will run with the working directory set to the root folder of the package, not the root folder of the entire project that the `artisan` command normally executes within.

By default, all package scripts are run in "development" mode. If you wish to run a script in "production" mode, add the `-f` or `--production` flag to the command.

If you wish to pass extra arguments or options to your script, you can add `--` to the end of the command. Any arguments or options added after the `--` argument are interpreted as arguments and options to be sent to the script itself.

### Update Node dependencies

```bash
php artisan npm:update [-p <package name>] [--npm <path to npm>]
```

The `npm:update` command will update Node dependencies for all registered packages.

This allows you to keep dependencies up to date, especially in the case of security fixes or breaking updates from your Node dependencies.
