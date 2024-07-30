# Asset Compilation - Vite

## Registration

By default, any plugin or theme containing a `vite.config.mjs` file at it's root will be automatically registered. Registered items can be viewed with the `vite:list` command.

The following sections document additional ways to configure a Vite package, these are only required if you need additional customization.

### Registering plugins

To register frontend assets to be compiled through Vite in your plugin, simply return an array with the package names as the keys and the package paths relative to the plugin's directory as the values to register from your [`Plugin.php`](../plugin/registration) registration file's `registerVitePackages()` method. See below example.

```php
public function registerVitePackages()
{
    return [
        'custom-package-name' => 'assets/js/build.vite.js',
    ];
}
```

### Registering themes

Registration of asset compilation of themes is even easier, and can be done by adding a `vite` definition to your [theme information file](../themes/development#theme-information-file) (`theme.yaml`).

```yaml
name: "Winter CMS Demo"
description: "Demonstrates the basic concepts of the frontend theming."
author: "Winter CMS"
homepage: "https://wintercms.com"
code: "demo"

vite:
    <name of package>: vite.config.mjs
```

The `vite` definition takes any number of registered packages as a YAML object, with the key being the name of the package as a kebab-case string and the location of your `vite.config.mjs` file relative to the theme's root directory.

For example, if you want to register two packages called `demo-theme-style` and `demo-theme-shop` located in the assets folder, you would use the following definition:

```yaml
name: "Winter CMS Demo"
description: "Demonstrates the basic concepts of the frontend theming."
author: "Winter CMS"
homepage: "https://wintercms.com"
code: "demo"

vite:
    demo-theme-style: assets/style/vite.config.mjs
    demo-theme-shop: assets/shop/vite.config.mjs
```

### Registering custom

You can configure a custom vite package that sits outside of plugins and themes.

```php
use System\Classes\Asset\PackageManager;

PackageManager::instance()->registerPackage(
    name: 'my-custom-package',
    path: '/path/to/vite.config.mjs',
    type: 'vite'
);
```

<div id="automatic-configuration"></div>

## Automatic Vite configuration

The command `vite:create` will allow you to automatically generate a basic Vite config which you can then build on top of.

```bash
php artisan vite:create <package name> [--tailwind] [--vue]
```

By default, the `vite:create` command will only generate the basic `vite.config.mjs` config file. If you would like Winter to pre-configure your package for a certain library / asset bundle, you can provide any of the following flags.

- `--tailwind` will configure your package for [tailwindcss](https://tailwindcss.com/)
- `--vue`  will configure your package for [vue.js](https://vuejs.org/)

For example, the following with configure the plugin `Acme.Example` with tailwind and create `plugins/acme/example/assets/src/acme-example.css` with a tailwind setup.

```bash
php artisan vite:create acme.example --tailwind
```

> **NOTE:** Winter will automatically pre-populate CSS/JS files with a basic setup of your chosen libraries. If you wish to only have the base configuration files generated then use the `--no-stubs` option.

## Manual Vite configuration

The Vite configuration file (`vite.config.mjs`) is a configuration file that manages the configuration of Laravel Vite itself. In conjunction with the `package.json` file that defines your dependencies, this file defines how Laravel Vite will compile your assets.

For more information, you can review [the Laravel docs](https://laravel.com/docs/11.x/vite) or the [Vite docs](https://vitejs.dev/config/).

Your `vite.config.mjs` file must include Vite as a requirement, and must also define the public path to the current directory, as follows:

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    build: {
        outDir: "assets/dist",
    },
    plugins: [
        laravel({
            publicDirectory: "assets/dist",
            input: [
                'assets/src/css/example.css',
                'assets/src/js/example.js',
            ],
            refresh: {
                paths: [
                    './**/*.htm',
                    './**/*.block',
                    'assets/src/**/*.css',
                    'assets/src/**/*.js',
                ]
            },
        }),
    ],
});
```

### Paths

When the `vite.config.mjs` file is evaluated, regardless of where you ran `vite:compile` from, the working directory is set to the parent directory of the `vite.config.mjs` file. That means that any relative paths used in the configuration will be relative to the current directory of the `vite.config.mjs` file.

## Vite vs. Mix

Vite works slightly differently to Mix, therefore this section documents the differences between the two and should help with anybody attempting to transition between the two.

### Vite server

When running `vite:watch`, Vite will create a new webserver hosted, by default, on port `5173`. If your environment is blocking specific ports, you must enable access to this port to use `vite:watch`.

By default, the vite server runs in http only mode, this may lead you to getting mixed content warnings in your browser. If this causes problems for you please see [the official vite docs](https://vitejs.dev/config/server-options#server-https).

### Loading Vite assets

Vite does not compile assets to a specific path like mix, instead you must use the helper functions to render your html imports.

The helper function takes 2 arguments, the first is a `array|string` of entry path(s). These entry paths must exist in the inputs of your `vite.config.js`. The second argument is the "package" you wish to load these entry points from, i.e. `theme-example` or `acme.plugin`.

- In `twig` this helper can be called via:

    ```twig
    {{ vite(['assets/css/example.css', 'assets/js/example.js'], 'acme.plugin') }}
    ```

- In `php` the `Vite` class offers a method for generating the imports:

    ```php
    \System\Classes\Vite::tags(['assets/css/example.css', 'assets/js/example.js'], 'acme.plugin')
    ```

- With the `AssetMaker` trait (i.e. in backend controllers, forms, etc.):

    ```php
    $this->addVite(['assets/css/example.css'], 'acme.plugin');
    // The second param here is not required, Winter will attempt to guess the plugin based on the current namespace
    $this->addVite(['assets/css/example.css']);
    ```

## Commands

### Install Node dependencies

```bash
php artisan vite:install [-p <package name>] [--npm <path to npm>]
```

The `vite:install` command will install Node dependencies for all registered Vite packages.

This command will add each registered package to the `workspaces.packages` property of your root `package.json` file and then run and display the results of `npm install` from your project root to install all of the dependencies for all of the registered packages at once.

You can optionally provide a `-p` or `--package` flag to install dependencies for one or more packages. To define multiple packages, simply add more `-p` flags to the end of the command.

If the command is run with a `-p` or `--package` flag and the provided package name is not already registered and the name matches a valid module, plugin, or theme package name (modules are prefixed with `module-$moduleDirectory`, themes are prefixed with `theme-$themeDirectory`, and plugins are simply `Author.Plugin`) then a `vite.config.mjs` file will be automatically generated for that package and will be included in future runs of any vite commands through the [automatic registration](#automatic-registration) feature.

The `--npm` flag can also be provided if you have a custom path to the `npm` program. If this is not provided, the system will try to guess where `npm` is located.

### List registered Vite packages

```bash
php artisan vite:list [--json]
```

The `vite:list` command will list all registered Vite packages found in the Winter installation. This is useful for determining if your plugin or theme is correctly registered.

The command will list all packages, as well as the directory for the asset and the configuration file that has been defined during registration.

A json formatted list of packages can be printed by specifying the `--json` flag.

### Compile a Vite packages

```bash
php artisan vite:compile [-p <package name>] [-f|--production] [-- <extra build options>]
```

The `vite:compile` command compiles all registered Vite packages, running each package through Laravel Vite for compilation.

By specifying the `-p` flag, you can compile one or more selected packages. To define multiple packages, simply add more `-p` flags to the end of the command.

The `--no-progress` flag can be added in order to suppress the vite progress output. Useful if you want to only view the webpack output.

By default, all packages are built in "development" mode. If you wish to compile in "production" mode, which may include more optimisations for production sites, add the `-f` or `--production` flag to the command.

The command will generate a report of all compiled files and their final size once complete.

If you wish to pass extra options to the Webpack CLI, for special cases of compilation, you can add `--` to the end of the command, followed by [any parameters](https://webpack.js.org/api/cli/) as per the Webpack CLI options.

### Watch a Vite package

```bash
php artisan vite:watch <package> [-f|--production] [-- <extra build options>]
```

The `vite:watch` command is similar to the `vite:compile` command, except that it remains active and watches for any changes made to files that would be affected by your compilation. When any changes are made, a compile is automatically executed. This is useful for development in allowing you to quickly make changes and review them in your browser.

With this command, only one package can be provided and watched at any one time.
