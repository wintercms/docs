# Asset Compilation - Mix

## Registration

By default, any plugin or theme containing a `winter.mix.js` file at it's root will be automatically registered. Registered items can be viewed with the `mix:list` command.

The following sections document additional ways to configure a Mix package, these are only required if you need additional customization.

### Registering plugins

To register frontend assets to be compiled through Mix in your plugin, simply return an array with the package names as the keys and the package paths relative to the plugin's directory as the values to register from your [`Plugin.php`](../plugin/registration) registration file's `registerMixPackages()` method. See below example.

```php
public function registerMixPackages(): array
{
    return [
        'custom-package-name' => 'assets/js/build.mix.js',
    ];
}
```

### Registering themes

Registration of asset compilation of themes is even easier, and can be done by adding a `mix` definition to your [theme information file](../themes/development#theme-information-file) (`theme.yaml`).

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

### Registering custom

You can configure a custom mix package that sits outside of plugins and themes.

```php
use System\Classes\CompilableAssets;

CompilableAssets::instance()->registerPackage(
    name: 'my-custom-package',
    path: '/path/to/winter.mix.js',
    type: 'mix'
);
```

## Automatic Mix configuration

The command `mix:config` will allow you to automatically generate a basic Mix config which you can then build on top of.

```bash
php artisan mix:config <package name> [--tailwind] [--vue] [--stubs]
```

By default, the `mix:config` command will only generate the basic `winter.mix.js` config file. If you would like Winter to pre-configure your package for a certain library, you can provide any of the following flags.

- `--tailwind` will configure your package for [tailwindcss](https://tailwindcss.com/)
- `--vue`  will configure your package for [vue.js](https://vuejs.org/)

The `--stubs` flag will tell Winter to automatically pre-populate css/js files with a basic setup of your chosen libraries.

For example, the following with configure the plugin `Acme.Example` with tailwind and create `plugins/acme/example/assets/src/acme-example.css` with a tailwind setup.

```bash
php artisan mix:config acme.example --tailwind --stubs
```

## Manual Mix configuration

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

## Commands

### Install Node dependencies

```bash
php artisan mix:install [-p <package name>] [--npm <path to npm>]
```

The `mix:install` command will install Node dependencies for all registered Mix packages.

This command will add each registered package to the `workspaces.packages` property of your root `package.json` file and then run and display the results of `npm install` from your project root to install all of the dependencies for all of the registered packages at once.

You can optionally provide a `-p` or `--package` flag to install dependencies for one or more packages. To define multiple packages, simply add more `-p` flags to the end of the command.

If the command is run with a `-p` or `--package` flag and the provided package name is not already registered and the name matches a valid module, plugin, or theme package name (modules are prefixed with `module-$moduleDirectory`, themes are prefixed with `theme-$themeDirectory`, and plugins are simply `Author.Plugin`) then a `winter.mix.js` file will be automatically generated for that package and will be included in future runs of any mix commands through the [automatic registration](#automatic-registration) feature.

The `--npm` flag can also be provided if you have a custom path to the `npm` program. If this is not provided, the system will try to guess where `npm` is located.

### List registered Mix packages

```bash
php artisan mix:list [--json]
```

The `mix:list` command will list all registered Mix packages found in the Winter installation. This is useful for determining if your plugin or theme is correctly registered.

The command will list all packages, as well as the directory for the asset and the configuration file that has been defined during registration.

A json formatted list of packages can be printed by specifying the `--json` flag.

### Compile a Mix packages

```bash
php artisan mix:compile [-p <package name>] [-f|--production] [-- <extra build options>]
```

The `mix:compile` command compiles all registered Mix packages, running each package through Laravel Mix for compilation.

By specifying the `-p` flag, you can compile one or more selected packages. To define multiple packages, simply add more `-p` flags to the end of the command.

The `--no-progress` flag can be added in order to suppress the mix progress output. Useful if you want to only view the webpack output.

By default, all packages are built in "development" mode. If you wish to compile in "production" mode, which may include more optimisations for production sites, add the `-f` or `--production` flag to the command.

The command will generate a report of all compiled files and their final size once complete.

If you wish to pass extra options to the Webpack CLI, for special cases of compilation, you can add `--` to the end of the command, followed by [any parameters](https://webpack.js.org/api/cli/) as per the Webpack CLI options.

### Watch a Mix package

```bash
php artisan mix:watch <package> [-f|--production] [-- <extra build options>]
```

The `mix:watch` command is similar to the `mix:compile` command, except that it remains active and watches for any changes made to files that would be affected by your compilation. When any changes are made, a compile is automatically executed. This is useful for development in allowing you to quickly make changes and review them in your browser.

With this command, only one package can be provided and watched at any one time.
