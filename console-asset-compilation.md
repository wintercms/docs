# Asset Compilation (Mix)

- [Introduction](#introduction)
- [Requirements](#requirements)
- [Registering a package](#registering-packages)
    - [Automatic registration](#automatic-registration)
    - [Registering plugin packages](#registering-plugins)
    - [Registering theme packages](#registering-themes)
- [Mix configuration](#mix-configuration)
- [Examples](#examples)
    - [Tailwind CSS](#examples-tailwind)
    - [Vue JS](#examples-vue)
- [Commands](#commands)
    - [Install Node dependencies](#mix-install)
    - [Update Node dependencies](#mix-update)
    - [List registered Mix packages](#mix-list)
    - [Compile a Mix package](#mix-compile)
    - [Watch a Mix package](#mix-watch)
    - [Run a package script](#mix-run)

<a name="introduction"></a>
## Introduction

Winter brings first-class support for handling Node-based compilation for frontend assets through the Mix commands. The comamnds use the [Laravel Mix](https://laravel-mix.com/) wrapper, a user-friendly and simple interface for setting up compilation of multiple types of frontend assets through Webpack and various libraries.

<a name="requirements"></a>
### Requirements

To take advantage of Mix asset compilation, you must have Node and the Node package manager (NPM) installed in your development environment. This will be dependent on your operating system - please review the [Download NodeJS](https://nodejs.org/en/download/) page for more information on installing Node.

[Laravel Mix](https://laravel-mix.com/) should also be present in the `package.json` file for any packages that will be using it (either in `dependencies` or a `devDependencies`) but if it is not specified in the project's `package.json` file then it can be optionally automatically added when running the [`mix:install`](#mix-install) command.

<a name="registering-packages"></a>
## Registering a package

Registering for asset compilation through Mix is very easy. Automatic registration should meet your needs most of the time, and if not there are several methods available to manually register Mix packages.

<a name="automatic-registration"></a>
### Automatic registration

By default, Winter will scan all available and enabled modules, plugins and themes for the presence of a `winter.mix.js` file under each extension's root folder (i.e. `modules/system/winter.mix.js`, `plugins/myauthor/myplugin/winter.mix.js`, or `themes/mytheme/winter.mix.js`).

If the `winter.mix.js` file is found, it will be automatically registered as a package with an automatically generated package name, and will show up when running the Mix commands. Most of the time, this should be all you need to do in order to get started with Laravel Mix asset compilation in Winter CMS.

<a name="registering-plugins"></a>
### Registering plugins

To register frontend assets to be compiled through Mix in your plugin, simply return an array with the package names as the keys and the package paths relative to the plugin's directory as the values to register from your [`Plugin.php`](../plugin/registration) registration file's `registerMixPackages()` method. See below example.

```php
public function registerMixPackages()
{
    return [
        'custom-package-name' => 'assets/js/build.mix.js',
    ];
}
```

<a name="registering-themes"></a>
### Registering themes

Registration of asset compilation of themes is even easier, and can be done by adding a `mix` definition to your [theme information file](../themes/development#theme-information) (`theme.yaml`).

```yaml
name: "Winter CMS Demo"
description: "Demonstrates the basic concepts of the frontend theming."
author: "Winter CMS"
homepage: "https://wintercms.com"
code: "demo"

mix:
    <name of package>: winter.mix.js
```

The `mix` definition takes any number of registered packages as a YAML object, with the key being the name of the package as a kebab-case string and the location of your `winter.mix.js` file relative to the theme's root directory.

For example, if you want to register two packages called `demo-theme-style` and `demo-theme-shop` located in the assets folder, you would use the following definition:

```yaml
name: "Winter CMS Demo"
description: "Demonstrates the basic concepts of the frontend theming."
author: "Winter CMS"
homepage: "https://wintercms.com"
code: "demo"

mix:
    demo-theme-style: assets/style/winter.mix.js
    demo-theme-shop: assets/shop/winter.mix.js
```

<a name="mix-configuration"></a>
## Mix configuration

The Mix configuration file (`winter.mix.js`) is a configuration file that manages the configuration of Laravel Mix itself. In conjunction with the `package.json` file that defines your dependencies, this file defines how Laravel Mix will compile your assets.

You can [review examples](https://laravel-mix.com/docs/6.0/examples) or the [full Mix API](https://laravel-mix.com/docs/6.0/api) at the [Laravel Mix website](https://laravel-mix.com).

Your `winter.mix.js` file must include Mix as a requirement, and must also define the public path to the current directory, as follows:

```js
const mix = require('laravel-mix');

// For assets in the current directory
// mix.setPublicPath(__dirname);

// For assets in a /assets subdirectory
mix.setPublicPath(__dirname + '/assets');

// Your mix configuration below
```

### Paths

When the `winter.mix.js` file is evaluated, regardless of where you ran `mix:compile` from, the working directory is set to the parent directory of the `winter.mix.js` file. That means that any relative paths used in the configuration will be relative to the current directory of the `winter.mix.js` file.

>**NOTE:** Winter's [path symbols](../services/helpers#path-symbols) are also supported in the `winter.mix.js` file.

<a name="examples"></a>
## Examples

Here are some examples of installing common frontend libraries for use with the asset compilation.

<a name="examples-tailwind"></a>
### Tailwind CSS

For themes that wish to use Tailwind CSS, include the `tailwindcss`, `postcss` and `autoprefixer` dependencies in your `package.json` file.

```bash
# Inside the project root folder
npm install --save-dev tailwindcss postcss autoprefixer

# Run the Tailwind initialisation
npx tailwindcss init
```

This will create a Tailwind configuration file (`tailwind.config.js`) inside your theme that you may [configure](https://tailwindcss.com/docs/installation) to your specific theme's needs.

Then, add a `winter.mix.js` configuration file that will compile Tailwind as needed:

```js
const mix = require('laravel-mix');
mix.setPublicPath(__dirname);

// Render Tailwind style
mix.postCss('assets/css/base.css', 'assets/css/theme.css', [
  require('postcss-import'),
  require('tailwindcss'),
]);
```

In the example above, we have a base CSS file that contains the Tailwind styling - `assets/css/base.css` - that will compile to a final compiled CSS file in `assets/css/theme.css`.

Your theme will now be ready for Tailwind CSS development.

<a name="examples-vue"></a>
### Vue in a backend controller

If you want to use Vue 3 in your plugin for backend controllers, you can follow these steps.

First, define Vue as a dependency in your plugin's `package.json`:

```
    "dependencies": {
        "vue": "^3.2.41"
    }
```

Then, add a `winter.mix.js` configuration file to your plugin directory:

```js
const mix = require('laravel-mix');
mix
    // compile javascript assets for plugin
    .js('assets/src/js/myplugin.js', 'assets/dist/js').vue({ version: 3 })
```

Next you can create your Vue source files in your plugin's assets/src/js/ directory:

```js
// assets/src/js/app.js

import { createApp } from 'vue'
import Welcome from './components/Welcome'

const app = createApp({})

app.component('welcome', Welcome)

app.mount('#app')
```

```js
// assets/src/js/components/Welcome.vue

<template>
    <h1>{{ title }}</h1>
</template>
<script>
export default {
    setup: () => ({
        title: 'Vue 3 in Laravel'
    })
}
</script>
```
Then install the NodeJS packages needed for your plugin in your plugin directory with the command `php artisan mix:install -p <my plugin>`.

Now if you comple your assets in the project root with `mix:compile` then mix will create the file in your plugin under assets/js/app.js which includes Vue and all other packages that you use in your components.

Next in the your controller's template file (eg. controllers/myvuecontroller/index.php) you can inclue this generated app.js, and render the content in the div#app:

```
<div id="app">
  <welcome/>
</div>

<script src="/plugins/foo/bar/assets/js/app.js"></script>
```

<a name="commands"></a>
## Commands

<a name="mix-install"></a>
### Install Node dependencies

```bash
php artisan mix:install [-p <package name>] [--npm <path to npm>]
```

The `mix:install` command will install Node dependencies for all registered Mix packages.

This command will add each registered package to the `workspaces.packages` property of your root `package.json` file and then run and display the results of `npm install` from your project root to install all of the dependencies for all of the registered packages at once.

You can optionally provide a `-p` or `--package` flag to install dependencies for one or more packages. To define multiple packages, simply add more `-p` flags to the end of the command.

If the command is run with a `-p` or `--package` flag and the provided package name is not already registered and the name matches a valid module, plugin, or theme package name (modules are prefixed with `module-$moduleDirectory`, themes are prefixed with `theme-$themeDirectory`, and plugins are simply `Author.Plugin`) then a `winter.mix.js` file will be automatically generated for that package and will be included in future runs of any mix commands through the [automatic registration](#automatic-registration) feature.

The `--npm` flag can also be provided if you have a custom path to the `npm` program. If this is not provided, the system will try to guess where `npm` is located.

<a name="mix-update"></a>
### Update Node dependencies

```bash
php artisan mix:update [-p <package name>] [--npm <path to npm>]
```

The `mix:update` command will update Node dependencies for all registered Mix packages.

This command operates very similar to `mix:install`, except that it only updates previously installed packages. This allows you to keep dependencies up to date, especially in the case of security fixes or breaking updates from your Node dependencies.

Please see the `mix:install` documentation for the available arguments and options.

<a name="mix-list"></a>
### List registered Mix packages

```bash
php artisan mix:list
```

The `mix:list` command will list all registered Mix packages found in the Winter installation. This is useful for determining if your plugin or theme is correctly registered.

The command will list all packages, as well as the directory for the asset and the configuration file that has been defined during registration.

<a name="mix-compile"></a>
### Compile a Mix packages

```bash
php artisan mix:compile [-p <package name>] [-f|--production] [-- <extra build options>]
```

The `mix:compile` command compiles all registered Mix packages, running each package through Laravel Mix for compilation.

By specifying the `-p` flag, you can compile one or more selected packages. To define multiple packages, simply add more `-p` flags to the end of the command.

By default, all packages are built in "development" mode. If you wish to compile in "production" mode, which may include more optimisations for production sites, add the `-f` or `--production` flag to the command.

The command will generate a report of all compiled files and their final size once complete.

If you wish to pass extra options to the Webpack CLI, for special cases of compilation, you can add `--` to the end of the command, followed by [any parameters](https://webpack.js.org/api/cli/) as per the Webpack CLI options.

<a name="mix-watch"></a>
### Watch a Mix package

```bash
php artisan mix:watch <package> [-f|--production] [-- <extra build options>]
```

The `mix:watch` command is similar to the the `mix:compile` command, except that it remains active and watches for any changes made to files that would be affected by your compilation. When any changes are made, a compile is automatically executed. This is useful for development in allowing you to quickly make changes and review them in your browser.

With this command, only one package can be provided and watched at any one time.

<a name="mix-run"></a>
### Run a package script

```bash
php artisan mix:run <package> <script> [-f|--production] [-- <extra script args>]
```

The `mix:run` command allows you to quickly run scripts defined in the `package.json` file of a Mix package.

```js
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
