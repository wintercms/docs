# Using Composer

- [Introduction](#introduction)
    - [Converting from a basic installation](#converting-from-basic-install)
    - [Development branch](#development-branch)
    - [Deployment best practices](#deployment-best-practices)
- [Installing a plugin or theme](#installing-plugin-theme)
- [Publishing blugins or themes](#publishing-products)
- [Package descriptions](#package-descriptions)
- [Marketplace builds](#marketplace-builds)
- [Using Laravel packages](#laravel-packages)
    - [Configuration files](#laravel-config-files)
    - [Aliases & service providers](#laravel-aliases-service-providers)
    - [Migrations & models](#laravel-migrations-models)

<a name="introduction"></a>
## Introduction

Using [Composer](https://getcomposer.org/) as an alternative package manager to using the standard one-click update manager is recommended for more advanced users and developers. See the console command on how to [first install Winter using composer](../console/commands#console-install-composer).

Composer is the de-facto standard for package management in the PHP ecosystem, and can handle the downloading, installation and management of Winter CMS plugins and themes, as well as third-party Laravel packages and vendor libraries.

<a name="converting-from-basic-install"></a>
### Converting from a basic installation

In order to use Composer with a Winter CMS instance that has been installed using the Wizard or simple CLI installation process, simply copy the latest [`tests/` directory](https://github.com/wintercms/winter/tree/develop/tests) and [`composer.json`](https://github.com/wintercms/winter/tree/develop/composer.json) file from [GitHub](https://github.com/wintercms/winter/tree/develop) into your Winter instance and then run `composer install` within the root directory of the project.

> **NOTE:** If you have made modifications to the files within the `modules` directory, these will be overwritten by Composer if an update to those modules is installed. It is recommended that you *do not* make modifications to the modules directly.

<a name="development-branch"></a>
### Development branch

If you plan on submitting pull requests to the Winter CMS project via GitHub, or are actively developing a project based on Winter CMS and want to stay up to date with the absolute latest version, we recommend switching your composer dependencies to point to the `develop` branch where all the latest improvements and bug fixes take place. Doing this will allow you to catch any potential issues that may be introduced (as rare as they are) right when they happen and get them fixed while you're still actively working on your project instead of only discovering them several months down the road if they eventually make it into production.

```
"winter/storm": "dev-develop as 1.1",
"winter/wn-system-module": "dev-develop",
"winter/wn-backend-module": "dev-develop",
"winter/wn-cms-module": "dev-develop",
"laravel/framework": "~6.0",
```

> **NOTE:** Do not commit the changes to `composer.json`, as this file is handled by the Winter CMS maintainers.

<a name="deployment-best-practices"></a>
### Deployment best practices

Using the following best practices with Composer and Winter CMS will make deployment of your Winter CMS installation much smoother:

- Store the `composer.lock` file in your source control. This file is ignored by Git by default in the `.gitignore` file included by Winter CMS, but should be deployed with your application to speed up the install and update process.
- Add a `.gitignore` file inside the `modules` folder to ignore all changes within this folder, as the modules will be installed and updated by Composer.
- Add a `.gitignore` file inside the `plugins` folder to ignore all changes within this folder if you install your plugins via Composer. You can optionally allow custom plugins that are only being used for that specific project.
- Use `composer install --no-dev` on your production instance to specifically exclude any "development" packages and libraries that won't be used in production.

<a name="installing-plugin-theme"></a>
## Installing a plugin or theme using Composer

Using Composer to install plugins and themes in Winter CMS allows a degree of control over the versions of plugins in use, making it easy to synchronise and deploy Winter CMS to multiple environments.

You may install a theme or plugin through Composer by [publishing the plugin or theme](#publishing-products) in [Packagist](https://packagist.org), the repository for Composer packages. Once the plugin or theme is published, you can include it in any Winter CMS project that has Composer enabled by running the following command within the root directory of the project:

```bash
composer require <your package name>

# Example:
#   composer require winter/wn-pages-plugin
```

If you wish to specify a particular version of the plugin or theme you want to include, you can specify the version as well:

```bash
composer require <your package name> "<version constraint>"

# Example:
#   composer require winter/wn-pages-plugin "^2.0.0"
```

You can also optionally specify a plugin or theme to be only included in "development" environments:

```bash
composer require --dev <your package name> "<version constraint>"

# Example:
#   composer require --dev winter/wn-builder-plugin "^2.0.0"
```

<a name="publishing-products"></a>
## Publishing plugins or themes

When publishing your plugins or themes to the marketplace, you may wish to also make them available via Composer. An example `composer.json` file for a plugin is included below:

    {
        "name": "winter/wn-demo-plugin",
        "type": "winter-plugin",
        "description": "Demo Winter CMS plugin",
        "keywords": ["winter", "cms", "demo", "plugin"],
        "license": "MIT",
        "authors": [
            {
                "name": "Winter CMS Maintainers",
                "url": "https://wintercms.com",
                "role": "Maintainer"
            }
        ],
        "require": {
            "php": ">=7.2",
            "composer/installers": "~1.0"
        }
    }

Be sure to start your package `name` with **wn-** and end it with **-plugin** or **-theme** respectively - this will help others find your package and is in  accordance with the [quality guidelines](../help/developer/guide#repository-naming).

The `type` field is a key definition for ensuring that your plugin or theme arrives at the correct location upon installation. Use the following types:

Product | Type
------- | -------------
Plugin  | `winter-plugin`
Theme   | `winter-theme`
Module  | `winter-module`

> **Reminder**: Be sure to specify any dependencies in your `composer.json` file as you would using the  `$require` property found in the [plugin registration file](../plugin/registration#dependency-definitions)

<a name="package-descriptions"></a>
## Package descriptions

There are many different moving parts that come together to make the Winter CMS platform work. Here we will describe the various packages you will likely encounter:

- **Modules** are the core packages that are included with Winter, you can think of them as "internal plugins" that provide core functionality. Modules use the package type `winter-module` and are located within the `/modules` directory. They are loaded manually via configuration and at least one module must be present in the `cms.loadModules` configuration item for the system to operate.

- **Plugins** extend the core functionality of Winter and are packages of type `winter-plugin`. They are located within the `/plugins` directory. The `System` module is responsible for the loading of plugins which happens automatically when found in the file system, unless they are explicitly disabled.

- **Themes** contain static file content that is used to manage the front end structure of your website and use the package type `winter-theme`. They are located within the `/themes` directory. The `Cms` module is responsible for managing themes and determining what theme is currently active.

- **Vendor** packages are included via Composer in either the project's `/vendor` directory or can sometimes be found in plugin-specific `/vendor` directories. The project vendor directory takes priority over and plugin vendor directories that appear in the system.

<a name="marketplace-builds"></a>
## Marketplace builds

When you publish your plugin or theme to the marketplace, the server will conveniently pull in all the packages defined in your composer file. This makes the product ready for others to use, even if they don't use composer. Here's how it works:

1. As a plugin or theme developer, you can define your external dependencies and packages in `composer.json`, including other plugins or themes.

2. The server will attempt to remove any core dependencies that are inherently available in the core, including Laravel, Winter and its related packages.

3. The server will run  `composer install` in your plugin or themes directory, pulling dependencies into the `vendor` directory, local to that package.

4. The files `composer.json` and `composer.lock` are then removed to prevent the package files from becoming duplicated and a potential double up of dependencies.

5. The final result is packaged up and ready for consumption by Winter CMS platforms using one-click updates.

It is a good idea not to include the `vendor` directory when publishing your plugin or theme to the marketplace, the server will handle this for you.

If you are developing with your plugin, you can run `composer update` from the root directory. A special package called `wikimedia/composer-merge-plugin` will scan the plugins directory and merge the dependencies in to the main composer file.

<a name="laravel-packages"></a>
## Using Laravel packages

When including Laravel packages in Winter CMS plugins there are a few things to take note of.

<a name="laravel-config-files"></a>
### Configuration files

Laravel packages will often provide configuration files, and they will usually come with the instructions to publish these config files to the project config folder, usually something like `php artisan vendor:publish --tag=config`.

However, this can create problems with Winter's plugin oriented design, since there would now be random config files in the core `/config` directory. In order to solve this problem, it is recommended that you proxy the included package's configuration through your plugin instead.

You may place this code in your Plugin registration file and call it from the  the `boot()` method.

    public function bootPackages()
    {
        // Get the namespace code of the current plugin
        $pluginNamespace = str_replace('\\', '.', strtolower(__NAMESPACE__));

        // Locate the packages to boot
        $packages = \Config::get($pluginNamespace . '::packages');

        // Boot each package
        foreach ($packages as $name => $options) {
            // Apply the configuration for the package
            if (
                !empty($options['config']) &&
                !empty($options['config_namespace'])
            ) {
                Config::set($options['config_namespace'], $options['config']);
            }
        }
    }

Now you are free to provide the packages configuration values the same way you would with regular plugin configuration values.

    return [
        // Laravel Package Configuration
        'packages' => [
            'packagevendor/packagename' => [
                // The accessor for the config item, for example,
                // to access via Config::get('purifier.' . $key)
                'config_namespace' => 'purifier',

                // The configuration file for the package itself.
                // Copy this from the package configuration.
                'config' => [
                    'encoding'      => 'UTF-8',
                    'finalize'      => true,
                    'cachePath'     => storage_path('app/purifier'),
                    'cacheFileMode' => 0755,
                ],
            ],
        ],
    ];

Now the package configuration has been included natively in Winter CMS and the values can be changed normally using the [standard configuration approach](../plugin/settings#file-configuration).

<a name="laravel-aliases-service-providers"></a>
### Aliases & service providers

By default, Winter CMS disables the loading of discovered packages through [Laravel's package discovery service](https://laravel.com/docs/6.x/packages#package-discovery), in order to allow packages used by plugins to be disabled if the plugin itself is disabled. Please note that packages defined in `app.providers` will still be loaded even if discovery is disabled.

> **NOTE:** It is possible to set `app.loadDiscoveredPackages` to `true` in the project configuration to enable automatic loading of these packages. This will result in packages being loaded, even if the plugin using them is disabled. This is **NOT RECOMMENDED.**

In order to manually register ServiceProviders and Aliases provided by external Laravel packages that are used by your plugins you should use the `App` facade and `AliasLoader` instance respectively:

```php
use App;
use Illuminate\Foundation\AliasLoader;
use System\Classes\Plugin as PluginBase;

class Plugin extends PluginBase
{
    public function register()
    {
        // Instantiate the AliasLoader
        $aliasLoader = AliasLoader::getInstance();

        // Register the aliases provided by the packages used by your plugin
        $aliasLoader->alias('Purifier', \Mews\Purifier\Facades\Purifier::class);

        // Register the service providers provided by the packages used by your plugin
        App::register(\Mews\Purifier\PurifierServiceProvider::class);
    }
}
```

<a name="laravel-migrations-models"></a>
### Migrations & models

Laravel packages that interact with the database will often include their own database migrations and Eloquent models. Ideally you should duplicate these migrations and models to your plugin's directory and then rebase the provided Model classes to extend the base `\Winter\Storm\Database\Model` class instead of the base Laravel Eloquent model class to take advantage of the extended technology features found in Winter.

You should also make an effort to rename the tables to prefix them with your plugin's author code and name. For example, a table with the name `posts` should be renamed to `winter_blog_posts`.
