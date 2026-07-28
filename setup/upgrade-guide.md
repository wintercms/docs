---
title: "Getting Started: Upgrade Guide"
description: "Upgrading your Winter CMS installation from v1.1 to v1.2."
---
# Upgrade Guide (v1.1 to v1.2)

With Winter's core goal of stability, upgrading your Winter CMS v1.1 site to the new v1.2 branch should be relatively straight-forward. However, as with all major releases, there may be some breaking changes or new requirements that may mean some adjustments to your projects.

We make every effort to document all new requirements and breaking changes, however, your project may have some very unique edge cases that we have not accounted for. If your project does not work after following the guide below, please feel free to [submit an issue on Github](https://github.com/wintercms/winter/issues/new/choose) or reach out to the [community on Discord](https://discord.gg/D5MFSPH6Ux) for assistance.

> **IMPORTANT:** If your site runs a version of Winter CMS v1.0, please upgrade to v1.1 first before going through this upgrade guide.

## New requirements

### Application server

- PHP 8.0.2 is now the minimum supported version. [PHP 7.4 and below is now unsupported](https://www.php.net/supported-versions.php).

### Database server

- The following versions of the database servers supported by Winter CMS are now the minimum versions required:
    - MySQL 5.7+ ([Version Policy](https://en.wikipedia.org/wiki/MySQL#Release_history))
    - MariaDB 10.2+ ([Version Policy](https://mariadb.org/about/#maintenance-policy))
    - PostgreSQL 9.6+ ([Version Policy](https://www.postgresql.org/support/versioning/))
    - SQLite 3.8.8+
    - SQL Server 2017+ ([Version Policy](https://docs.microsoft.com/en-us/lifecycle/products/?products=sql-server))

### Dependencies

- The following dependencies are now used in Winter CMS 1.2. These will automatically be installed when upgrading.
    - Laravel: 9.x
    - Laravel Tinker: 2.7
    - Twig: 3.x
    - Symfony\Yaml: 5.1
    - PHPUnit: 9.5.8
    - Mockery: 1.4.4
    - Assetic: 3.0

## Composer updates

> **Impacts:** All users.

If you installed your project via Composer, you must make the following changes to the `composer.json` file in the root directory of your project.

Within the `require` section:

```json
"php": "^8.0.2",
"winter/storm": "~1.2.0",
"winter/wn-system-module": "~1.2.0",
"winter/wn-backend-module": "~1.2.0",
"winter/wn-cms-module": "~1.2.0",
"laravel/framework": "^9.1",
"wikimedia/composer-merge-plugin": "~2.0.1"
```

Within the `require-dev` section:

```json
"phpunit/phpunit": "^9.5.8",
"mockery/mockery": "^1.4.4",
"fakerphp/faker": "^1.9.2",
"squizlabs/php_codesniffer": "^3.2",
"php-parallel-lint/php-parallel-lint": "^1.0",
"dms/phpunit-arraysubset-asserts": "^0.1.0|^0.2.1"
```

You must also remove the entire `autoload-dev` section below, unless you are using this section for your own requirements.

```json
"autoload-dev": {
    "classmap": [
        "tests/concerns/InteractsWithAuthentication.php",
        "tests/fixtures/backend/models/UserFixture.php",
        "tests/TestCase.php",
        "tests/PluginTestCase.php"
    ]
},
```

Once done, run `composer update` to install all the required dependencies and upgrade Winter CMS to version 1.2.

## Tests folder

> **Impacts:** All users.

The `tests` folder in the root folder of your project is no longer required, as all Winter CMS tests are now stored in the modules that those tests relate to. You may remove the entire folder, unless you are using the folder to store your own application tests.

## Server script

> **Impacts:** All users.

The `server.php` file located in your project root folder is no longer required for `php artisan serve` to function. You may remove this if you wish, however, there is no harm to your project if you leave it there.

## Configuration file changes

> **Impacts:** Most users.

There have been significant changes to the default configuration files, mainly in those that are based on the Laravel framework's configuration files. You should review the changes to the [default configuration files](https://github.com/wintercms/winter/tree/1.2/config) and implement any changes as desired.

The following items have the highest likelihood of having an impact on your projects:

### config/app.php

- An application key is no longer provided by default. If your application does not yet have one set, run `php artisan key:generate` in your project root folder.
- `Illuminate\Http\Request::HTTP_X_FORWARDED_ALL` has been removed in Symfony 6, but it's referenced with no intermediate layer in the default `app.php` config file for the `testedProxyHeaders` configuration from v1.1.4 to v1.1.8. See https://github.com/wintercms/storm/commit/fcecefda3fd3966310306b5799852c53d6330a64 for more details. If you are using this value for `trustedProxyHeaders`, change this to the string `HEADER_X_FORWARDED_ALL`.

### config/database.php

- The `database.useConfigForTesting` configuration is no longer supported. Use `config/testing/database.php` to override testing defaults instead.
- Setting a `varcharmax` on `mysql` database connections is no longer supported as the minimum required version of MySQL now supports 255 character UTF8 strings by default.

### config/mail.php

- The structure has changed - update your local copy to match the one [now provided on the 1.2 branch](https://github.com/wintercms/winter/blob/1.2/config/mail.php). The old structure may work, but it has been reported in some cases that it does not, so it is recommended to update it to reflect the new structure.

### config/filesystems.php

- The `local` disk currently needs a `visibility => 'public'` setting in order for the files and folders under `/storage/app/resized` to be publicly accessible when using the image resizer.

## Third-party email delivery providers

> **Impacts:** Users of third-party email providers, plugin developers.

- Built-in support for SES, Postmark, Mailgun, Mandrill, SendGrid, & SparkPost mail drivers has been removed. Use the applicable first-party driver plugins as needed:
    - [Winter.DriverAWS](https://github.com/wintercms/wn-driveraws-plugin)
    - [Winter.DriverMailgun](https://github.com/wintercms/wn-drivermailgun-plugin)
    - [Winter.DriverMandrill](https://github.com/wintercms/wn-drivermandrill-plugin)
    - [Winter.DriverPostmark](https://github.com/wintercms/wn-driverpostmark-plugin)
    - [Winter.DriverSendGrid](https://github.com/wintercms/wn-driversendgrid-plugin)
    - [Winter.DriverSparkPost](https://github.com/wintercms/wn-driversparkpost-plugin)
- Review the [SymfonyMailer upgrade guide](https://laravel.com/docs/9.x/upgrade#symfony-mailer) if you interact with mail message objects directly, as several methods and return types have changed.

## Storage & files

> **Impacts:** Rackspace storage users, plugin developers.

- The Rackspace Flysystem driver / adapter is no longer supported.
- `Symfony\Component\HttpFoundation\File\UploadedFile->getClientSize()` has been removed. Use `getSize()` instead.
- The `getAdapter()` method on Storage disks is no longer present. `getPathPrefix()` & `setPathPrefix()` methods have been added to the `$disk` instances if desired.

## Localization changes

> **Impacts:** Plugin developers.

The `translator.beforeResolve` event has been removed for performance reasons. `Lang::set($key, $value, $locale)` can be used as a replacement.

### Overriding a specific key

Before Winter v1.2:

```php
 Event::listen('translator.beforeResolve', function ($key, $replaces, $locale) {
    if ($key === 'validation.reserved') {
        return Lang::get('winter.builder::lang.validation.reserved');
    }
});
```

From Winter v1.2:

```php
Lang::set('validation.reserved', Lang::get('winter.builder::lang.validation.reserved'));
```

### Overriding an entire namespace

If you were using the event to extend / override an entire namespaced key (for example in order to share localization overrides that would normally be present in a project's `lang` override folder at the project root between multiple projects via a plugin), then you could switch your code to use the following example:

```php
Lang::set('winter.builder::lang', require __DIR__ . '/lang/en-ca/lang.php', 'en-ca');
```

## Facades

> **Impacts:** Plugin developers.

- Facades must return string keys rather than objects (See https://github.com/laravel/framework/pull/38017).
- `Winter\Storm\Support\Facades\Str` facade has been removed. Use `Winter\Storm\Support\Str` directly instead. The `Str` alias now points to `Winter\Storm\Support\Str` instead.

## Laravel packages

> **Impacts:** Plugin developers.

The version of Laravel has been changed from 6.x LTS to 9.x. If you are using packages made for Laravel, you may have to go through and update them to a version compatible with Laravel 9.x.

## Unit testing

> **Impacts:** Plugin developers that use test cases.

Unit testing should now be conducted using the `php artisan winter:test` command, as opposed to running unit tests directly on `phpunit`. This ensures that the correct bootstrap is used, as well as the necessary environment configuration is created.

If you have unit tests that extend either the base `TestCase` or `PluginTestCase` classes, these must now extend `\System\Tests\Bootstrap\TestCase` and `\System\Tests\Bootstrap\PluginTestCase` instead, respectively.

If your plugin is intended to work (and test) on both Winter 1.1 and 1.2, you may create a stub class in your test cases that will use the corresponding base test case class depending on the Winter CMS version in use.

```php
// Create a stub BaseTestCase that extends the correct plugin test case file depending on Winter version
if (class_exists('System\Tests\Bootstrap\PluginTestCase')) {
    class BaseTestCase extends \System\Tests\Bootstrap\PluginTestCase
    {
    }
} else {
    class BaseTestCase extends \PluginTestCase
    {
    }
}

class MyTestCase extends BaseTestCase
```

## Storm library internals

> **Impacts:** Some users and plugin developers.

Version 1.2 of Winter includes a large code refactoring and documentation cleanup for the Storm library, to ensure that our base functionality is fully documented and works in a consistent and expected way. In addition, this brings our Storm library closer to the base Laravel functionality, which should make upgrades quicker and less painful in the future.

While we have ensured that the potential for breaking changes is low, there may be some cases of breaking functionality if the functionality used an undocumented or incorrectly-documented API. We will list all the changes below, grouped by the "package" within the Storm library:

- [\Winter\Storm\Database](#database)
- [\Winter\Storm\Extension](#extension)
- [\Winter\Storm\Filesystem](#filesystem)
- [\Winter\Storm\Foundation](#foundation)
- [\Winter\Storm\Halcyon](#halcyon)
- [\Winter\Storm\Html](#html)
- [\Winter\Storm\Parse](#parse)
- [\Winter\Storm\Support\Testing](#supporttesting)

### Database

- To prevent unsafe model instantiating, the model constructors are now forced to only allow a single `$attributes` parameter via an interface (`\Winter\Storm\Database\ModelInterface` and `\Winter\Storm\Halcyon\ModelInterface`). This will ensure that calls like `Model::make()` or `Model::create()` will execute correctly. It is possible that some people might have used additional parameters for their model constructors - these will no longer work and must be moved to another method.
- Due to the above, Pivot model construction has been rewritten. The constructor used to allow 4 parameters but now only allows the one `$attributes` parameter as per the Model class. Construction now happens more closely to Laravel's format of calling a `Pivot::fromAttributes()` static method. If you previously used `new Pivot()` to create a pivot model, switch to using `Pivot::fromAttributes()` instead.
- The `MorphToMany` class now extends the `MorphToMany` class from Laravel, as opposed to the `BelongsToMany` class in Winter. This prevents repeated code in the Winter `MorphToMany` class and maintains covariance with Laravel. This will mean that it will no longer inherit from the Winter `BelongsToMany` class. To allow for this, we have converted most of the overridden `BelongsToMany` functionality in Winter into a trait (`Concerns\BelongsOrMorphsToMany`). The `BelongsToMany` relation class now also uses this trait.
- The relation traits found in `src/Database/Relations` have been moved to `src/Database/Relations/Concerns`, in order to keep just the actual relation classes within the `src/Database/Relations` directory. In the unlikely event that you are using a relation trait directly, please rename the trait classes to the following:
    - `Winter\Storm\Database\Relations\AttachOneOrMany` to `Winter\Storm\Database\Relations\Concerns\AttachOneOrMany`
    - `Winter\Storm\Database\Relations\DeferOneOrMany` to `Winter\Storm\Database\Relations\Concerns\DeferOneOrMany`
    - `Winter\Storm\Database\Relations\DefinedConstraints` to `Winter\Storm\Database\Relations\Concerns\DefinedConstraints`
    - `Winter\Storm\Database\Relations\HasOneOrMany` to `Winter\Storm\Database\Relations\Concerns\HasOneOrMany`
    - `Winter\Storm\Database\Relations\MorphOneOrMany` to `Winter\Storm\Database\Relations\Concerns\MorphOneOrMany`

### Extension

- Previously, the `Winter\Storm\Extension\Extendable::extendClassWith()` method returned the current class if the extension name provided was an empty string. This appears to be a code smell, so this has been changed to throw an Exception.

### Filesystem

- The `Filesystem::symbolizePath` method's `$default` parameter now accepts a `string`, `bool` or `null`. Only in the case of `null` will the method return the original path - the given `$default` will be used in all other cases. This is the same as the original functionality, but we're documenting it in case you have customised this method in some fashion.
- The `Filesystem::isPathSymbol` method previously returned the path symbol used, as a string, if one was found and `false` if not found. However, the docblock stipulated, as well as the method name itself implied, that this is a boolean check function, so we have converted this to a straight boolean response - `true` if a path symbol is used, otherwise `false`.
- Many classes within the `Filesystem` namespace have had type hinting and return types added to enforce functionality and clarify documentation. If you extend any of these classes in your plugins, you may need to update your method signatures.

### Foundation

- The `Winter\Storm\Foundation\Application` class callback methods `before` and `after` were documented as `void` methods, but returned a value. These no longer return a value.

### Halcyon

- To prevent unsafe model instantiating, the model constructors are now forced to only allow a single `$attributes` parameter via an interface (`\Winter\Storm\Database\ModelInterface` and `\Winter\Storm\Halcyon\ModelInterface`). This will ensure that calls like `Model::make()` or `Model::create()` will execute correctly. It is possible that some people might have used additional parameters for their model constructors - these will no longer work and must be moved to another method.
- The Halcyon Builder `insert` method now returns an `int` representing the created model's filesize, not a `bool`.
- The Halcyon Builder `insert` method requires the `$values` parameter to actually contain variables and will throw an Exception if an empty array is provided. Previously, this was silently discarded and returned `true` (although this would not actually save the file).

### HTML

- The `Winter\Storm\Html\FormBuilder` class has had type hinting and return types added to enforce functionality and clarify documentation. If you extend this class in your plugin, you may need to make changes to your method signatures if you overwrite the base functionality.

### Parse

- The `Bracket` class constructor is now `final` to prevent unsafe static calls to the `parse` static method.
- The `Bracket::parseString()` method previously returned `false` if a string was not provided for parsing, or the string was empty after trimming. Since the signature calls for a string, we won't check for this anymore. If the string is empty, an empty string will be returned.
- The `markdown.beforeParse` event in the `Markdown` class sent a single `MarkdownData` instance as a parameter to the listeners. It now sends an array with the `MarkdownData` instance as the only value, to meet the signature requirements for events.
- The `Syntax\FieldParser` class constructor has been made `final` to prevent unsafe static calls to the `parse` statuc method.
- The `$template` parameter for the `Syntax\FieldParser` class constructor was originally optional. Since there is no purpose for this, and no way to populate the template after the fact, this has been made required.
- The `Syntax\Parser` class constructor has been made `final` to prevent unsafe static calss to the `parse` static method.
- The `$template` parameter for the `Syntax\Parser` class constructor was originally optional. Since there is no purpose for this, and no way to populate the template after the fact, this has been made required.

### Support\Testing

- The `Winter\Storm\Support\Testing\Fakes\MailFake::queue()` method has had its signature re-arranged to remain compatible with Laravel's `MailFake` class, with the `$queue` parameter being moved to the second parameter. The new signature is `($view, $queue, $data, $callback)`.

## Upgrade guides for dependencies

> **Impacts:** Informational only.

- [PHP 8.0](https://www.php.net/manual/en/migration80.php)
- [PHP 8.1](https://www.php.net/manual/en/migration81.php)
- [Laravel 7](https://laravel.com/docs/7.x/upgrade)
- [Laravel 8](https://laravel.com/docs/8.x/upgrade)
- [Laravel 9](https://laravel.com/docs/9.x/upgrade)
- [SwiftMailer to Symfony Mailer](https://github.com/laravel/framework/pull/38481)
- [Symfony v5](https://github.com/symfony/symfony/blob/5.4/UPGRADE-5.0.md)
- [Symfony v6](https://github.com/symfony/symfony/blob/6.0/UPGRADE-6.0.md)
- [Twig 3.0](https://twig.symfony.com/doc/2.x/deprecated.html) ([changelog](https://github.com/twigphp/Twig/blob/3.x/CHANGELOG) & [announcement](https://symfony.com/blog/preparing-your-applications-for-twig-3))
- [Flysystem v2.0](https://flysystem.thephpleague.com/docs/upgrade-from-1.x/) & https://github.com/laravel/framework/pull/33612
- [Flysystem v3.0](https://flysystem.thephpleague.com/docs/what-is-new/) & https://github.com/laravel/framework/pull/40411
