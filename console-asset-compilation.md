# Asset Compilation (Mix)

- [Introduction](#introduction)
- [Registering a package](#registering-packages)
    - [First steps](#first-steps)
    - [Plugins](#registering-plugins)
    - [Themes](#registering-themes)
    - [Mix configuration](#mix-configuration)
    - [Examples](#examples)
- [Commands](#commands)
    - [Install Node dependencies](#mix-install)
    - [List registered Mix packages](#mix-list)
    - [Compile a Mix package](#mix-compile)
    - [Watch a Mix package](#mix-watch)

<a name="introduction"></a>
## Introduction

Winter brings first-class support for handling Node-based compilation for front-end assets through the Mix commands. The comamnds use the [Laravel Mix](https://laravel-mix.com/) wrapper, a user-friendly and simple interface to setting up compilation of multiple types of front-end asset through Webpack and various libraries.

<a name="registering-packages"></a>
## Registering a package

Registering for asset compilation through Mix is very easy, and only changes slightly depending on whether the asset compilation is for a plugin or a theme.

<a name="first-steps"></a>
### First steps

To take advantage of asset compilation, you must have Node and the Node package manager (NPM) installed in your development environment. This will be dependent on your operating system - please review the [Download NodeJS](https://nodejs.org/en/download/) page for more information on installing Node.

You will need to create a package configuration file (`package.json`) in your plugin or theme that defines the dependencies that you require for your assets to compile. In the majority of cases, you will only need Laravel Mix as a dependency.

We recommend that the `package.json` file is located in the root folder of your plugin or theme. You can use the `npm init` command in your CLI to quickly create this file:

```bash
# For a plugin
cd plugins/author/myplugin
# For a theme
cd themes/mytheme

# Run the initialization
npm init

# You will be asked a series of questions:
# - package name: [your theme or plugin name]
# - version: [a version number, default 1.0.0]
# - description: [enter a description for your theme or plugin]
# - entry point: index.js
# - test command: *keep blank*
# - git repository: [enter in your git repository URL, or keep blank]
# - keywords: [enter in some keywords on what your theme or plugin does, or keep blank]
# - author: [enter in your name and email in the format "Your Name <your@email.com>"]
# - license: [enter a license - see https://docs.npmjs.com/cli/v8/configuring-npm/package-json#license for licenses]
# - Press ENTER if OK
```

Once your `package.json` file is created, you can then add dependencies to it. One that is required for these commands is Laravel Mix:

```bash
npm i --save-dev laravel-mix
```

If you edit the `package.json` file directly, you will need to ensure that the dependencies are installed before using the commands. You can use the [`mix:install`](#mix-install) command to install all dependencies.

```bash
# In your project root folder
php artisan mix:install
```

<a name="registering-plugins"></a>
### Registering plugins

To register front-end assets to be compiled through Mix in your plugin, you can use the `System\Classes\MixAssets` class to register the package. The following code can be added to your [`Plugin.php`](../plugin/registration) registration file's `boot()` method to register a callback method that registers the package when asset compilation is processed:

```php
public function boot()
{
    \System\Classes\MixAssets::registerCallback(function ($mix) {
        $mix->registerPackage('<name of package>', '<directory where package.json is located>');
    });
}
```

The `registerPackage()` method takes 3 arguments: the name of the package which should be a kebab-case string that represents your package name, the directory where to find the `package.json` file and the Mix configuration file and an optional third parameter which defines the filename of your Mix configuration file. By default, we use `winter-mix.js` as this filename, but you may rename it to any other filename that you wish and define it as the third argument.

For example, if you want to register a package called `my-plugin`, and the `package.json` and `winter-mix.js` file can be located in the `plugins/author/myplugin/assets` folder, you would use the following code block:

```php
public function boot()
{
    \System\Classes\MixAssets::registerCallback(function ($mix) {
        $mix->registerPackage('my-plugin', '~/plugins/author/myplugin/assets');
    });
}
```

<a name="registering-themes"></a>
### Registering themes

Registration of asset compilation of themes is even easier, and can be done by adding a `mix` definition to your [theme information file](../themes-development#version-file) (`theme.yaml`).

```yaml
name: "Winter CMS Demo"
description: "Demonstrates the basic concepts of the frontend theming."
author: "Winter CMS"
homepage: "https://wintercms.com"
code: "demo"

mix:
    <name of package>: winter-mix.js
```

The `mix` definition takes any number of registered packages as a YAML object, with the key being the name of the package as a kebab-case string and the location of your `winter-mix.js` file relative to the theme root directory.

For example, if you want to register two packages called `demo-theme-style` and `demo-theme-shop` located in the assets folder, you would use the following definition:

```yaml
name: "Winter CMS Demo"
description: "Demonstrates the basic concepts of the frontend theming."
author: "Winter CMS"
homepage: "https://wintercms.com"
code: "demo"

mix:
    demo-theme-style: assets/style/winter-mix.js
    demo-theme-shop: assets/shop/winter-mix.js
```

<a name="mix-configuration"></a>
### Mix configuration

The Mix configuration file (`winter-mix.js`) is a configuration file that manages the configuration of Laravel Mix itself. In conjunction with the `package.json` file that defines your dependencies, this file defines how Laravel Mix will compile your assets.

You can [review examples](https://laravel-mix.com/docs/6.0/examples) or the [full Mix API](https://laravel-mix.com/docs/6.0/api) at the [Laravel Mix website](https://laravel-mix.com).

Your `winter-mix.js` file must include Mix as a requirement, and must also define the public path to the current directory, as follows:

```js
const mix = require('laravel-mix');
mix.setPublicPath(__dirname);

// Your mix configuration below
```

<a name="examples"></a>
### Examples

Here are some examples of installing common front-end libraries for use with the asset compilation.

#### Tailwind CSS

For themes that wish to use Tailwind CSS, include the `tailwindcss`, `postcss` and `autoprefixer` dependencies in your `package.json` file.

```bash
# Inside the project root folder
npm install --save-dev tailwindcss postcss autoprefixer

# Run the Tailwind initialisation
npx taildwindcss init
```

This will create a Tailwind configuration file (`tailwind.config.js`) inside your theme that you may [configure](https://tailwindcss.com/docs/installation) to your specific theme's needs.

Then, add a `winter-mix.js` configuration file that will compile Tailwind as needed:

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

<a name="commands"></a>
## Commands

### Install Node dependencies

```bash
php artisan mix:install [-p <package name>] [--npm <path to npm>] 
```

The `mix:install` command will install Node dependencies for all registered Mix packages. This command will cycle through each registered package, running the `npm install` command and displaying the output of this command.

You can optionally provide a `-p` flag to install dependencies for one or more packages. To define multiple packages, simply add more `-p` flags to the end of the command.

The `--npm` flag can also be provided if you have a custom path to the `npm` program. If this is not provided, the system will try to guess where `npm` is located.

### List registered Mix packages

```bash
php artisan mix:list
```

The `mix:list` command will list all registered Mix packages found in the Winter installation. This is useful for determining if your plugin or theme is correctly registered.

The command will list all packages, as well as the directory for the asset and the configuration file that has been defined during registration.

### Compile a Mix packages

```bash
php artisan mix:compile [-p <package name>] [-f|--production] [-- <extra build options>]
```

The `mix:compile` command compiles all registered Mix packages, running each package through Laravel Mix for compilation.

By specifying the `-p` flag, you can compile one or more selected packages. To define multiple packages, simply add more `-p` flags to the end of the command.

By default, all packages are built in "development" mode. If you wish to compile in "production" mode, which may include more optimisations for production sites, add the `-f` or `--production` flag to the command.

The command will generate a report of all compiled files and their final size once complete.

If you wish to pass extra options to the Webpack CLI, for special cases of compilation, you can add `--` to the end of the command, followed by [any parameters](https://webpack.js.org/api/cli/) as per the Webpack CLI options.

### Watch a Mix package

```bash
php artisan mix:watch <package> [-f|--production] [-- <extra build options>]
```

The `mix:watch` command is similar to the the `mix:compile` command, except that it remains active and watches for any changes made to files that would be affected by your compilation. When any changes are made, a compile is automatically executed. This is useful for development in allowing you to quickly make changes and review them in your browser.

With this command, only one package can be provided and watched at any one time.