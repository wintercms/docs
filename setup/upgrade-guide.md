---
title: "Getting Started: Upgrade Guide"
description: "Upgrading your Winter CMS installation from v1.2 to v1.3."
---
# Upgrade Guide (v1.2 to v1.3)

Winter CMS v1.3 moves the platform from Laravel 9 to Laravel 12. Most of the work has been done inside Winter itself, so for many projects the upgrade comes down to updating `composer.json`, reviewing a few configuration files and running `composer update`. However, jumping three major versions of Laravel also brings in new major versions of Symfony, Carbon, Monolog and PHPUnit, and some of their changes reach your own code and plugins.

This guide lists the new requirements and the breaking changes in Winter, together with the changes from Laravel 10, 11 and 12 and their dependencies that affect Winter projects. Changes from those upgrade guides that do not apply to Winter (for example, the new Laravel 11 application structure) are left out.

We make every effort to document all new requirements and breaking changes, however, your project may have some very unique edge cases that we have not accounted for. If your project does not work after following the guide below, please feel free to [submit an issue on Github](https://github.com/wintercms/winter/issues/new/choose) or reach out to the [community on Discord](https://discord.gg/D5MFSPH6Ux) for assistance.

> **IMPORTANT:** If your site runs a version of Winter CMS older than v1.2, please upgrade to v1.2 first before going through this upgrade guide.

## New requirements

### Application server

- PHP 8.2 is now the minimum supported version.
- Composer 2.2 or above is required.
- If you use the HTTP client (`Http` facade), curl 7.34.0 or above is required.

### Database server

- The following versions of the database servers supported by Winter CMS are now the minimum versions required:
    - MySQL 5.7+ ([Version Policy](https://en.wikipedia.org/wiki/MySQL#Release_history))
    - MariaDB 10.3+ ([Version Policy](https://mariadb.org/about/#maintenance-policy))
    - PostgreSQL 10.0+ ([Version Policy](https://www.postgresql.org/support/versioning/))
    - SQLite 3.35.0+
    - SQL Server 2017+ ([Version Policy](https://docs.microsoft.com/en-us/lifecycle/products/?products=sql-server))

> **NOTE:** Laravel 12 documents SQLite 3.26.0 as its minimum, but Winter requires SQLite 3.35.0. The `winter:up` command stops with an error on older SQLite versions. This includes plugin test runs, which use an in-memory SQLite database.

### Dependencies

- The following dependencies are now used in Winter CMS 1.3. These will automatically be installed when upgrading.
    - Laravel: 12.x
    - Laravel Tinker: 2.8
    - Symfony: 7.x (Console, Process, HTTP Foundation and the other components installed by Laravel)
    - Carbon: 3.x
    - Monolog: 3.x
    - Doctrine DBAL: 3.x
    - PHPUnit: 11.x
    - Twig: 3.x (unchanged)

## Composer updates

> **Impacts:** All users.

If you installed your project via Composer, you must make the following changes to the `composer.json` file in the root directory of your project.

Within the `require` section:

```json
"php": "^8.2",
"winter/storm": "~1.3.0",
"winter/wn-system-module": "~1.3.0",
"winter/wn-backend-module": "~1.3.0",
"winter/wn-cms-module": "~1.3.0",
"laravel/framework": "^12.0",
"wikimedia/composer-merge-plugin": "~2.1.0"
```

Within the `require-dev` section:

```json
"phpunit/phpunit": "^11.0",
"mockery/mockery": "^1.6",
"fakerphp/faker": "^1.9.2",
"squizlabs/php_codesniffer": "^3.2",
"php-parallel-lint/php-parallel-lint": "^1.0"
```

The `dms/phpunit-arraysubset-asserts` package does not have a release that supports PHPUnit 11. Remove it unless your own tests use `assertArraySubset()`.

Other Laravel packages you require may also need a new version that supports Laravel 12 (see [Laravel packages](#laravel-packages) below).

Once done, run `composer update` to install all the required dependencies and upgrade Winter CMS to version 1.3.

## Configuration file changes

> **Impacts:** Most users.

There are only a few changes to the default configuration files. You should review the [default configuration files](https://github.com/wintercms/winter/tree/1.3/config) and implement any changes as desired.

### config/app.php

- A new `env` setting holds the application environment (`APP_ENV`).
- The `locale`, `fallback_locale` and `faker_locale` settings can now be set with the `APP_LOCALE`, `APP_FALLBACK_LOCALE` and `APP_FAKER_LOCALE` environment variables.
- A new `previous_keys` setting allows you to rotate your application key. List your old keys, separated by commas, in the `APP_PREVIOUS_KEYS` environment variable, and data encrypted with them can still be decrypted.

```php
'env' => env('APP_ENV', 'production'),

'locale' => env('APP_LOCALE', 'en'),
'fallback_locale' => env('APP_FALLBACK_LOCALE', 'en'),
'faker_locale' => env('APP_FAKER_LOCALE', 'en_US'),

'key' => env('APP_KEY'),

'previous_keys' => [
    ...array_filter(
        explode(',', (string) env('APP_PREVIOUS_KEYS', ''))
    ),
],
```

### config/hashing.php

- Add `'rehash_on_login' => false`. Laravel 11 introduced automatic password rehashing on login, which is enabled by default when this setting is missing. Winter's backend and frontend authentication do not use it, but it would apply to any Laravel authentication guard used by a plugin.

### Settings that are missing from your configuration

Winter only reads your own configuration files. It does not merge in the default configuration files of the Laravel framework, so a setting that was added in Laravel 10, 11 or 12 is not active until you add it to your own configuration files. Please keep the following in mind when you compare your configuration with the Laravel 12 defaults:

- Do not add `'serve' => true` to the `local` disk in `config/filesystems.php`. Laravel 12 uses this setting to serve the files of a disk through a route. Because Winter's `local` disk is public, Laravel would serve every file in `storage/app`, including protected uploads, without checking a signature.
- Do not add `'verify' => true` to the `bcrypt` or `argon` settings in `config/hashing.php` unless every password hash in your database uses that algorithm. With this setting, `Hash::check()` throws an exception for any hash made with another algorithm instead of returning `false`.

### Cache key prefixes

> **Impacts:** Users of the Redis, Memcached and DynamoDB cache stores.

Laravel no longer adds a `:` to the end of the cache prefix. With Winter's default `CACHE_PREFIX`, a cache key such as `winter_cache:settings` becomes `winter_cachesettings` after the upgrade, which means your application starts with an empty cache and the old keys stay in the store until they expire. If you want to keep the old key names, set the `CACHE_PREFIX` environment variable to a value that ends with a `:`.

## Console commands

> **Impacts:** Plugin developers.

### Command names and registration

Symfony Console 7 no longer uses the static `$defaultName` property of a command, and the `getDefaultName()` method now returns `null` unless the command class has an `#[AsCommand]` attribute. Commands still get their name from their `$signature` or `$name` property, so the `$defaultName` property can simply be removed.

However, if your plugin registers commands using `getDefaultName()`, every one of those commands is registered under the same key and only the last one registered will be available. Nothing warns you about this: the other commands are simply missing from `php artisan list`, and scheduled tasks that run them fail.

```php
// Before Winter v1.3: each command was registered under its own name
$this->registerConsoleCommand(ImportProducts::getDefaultName(), ImportProducts::class);
$this->registerConsoleCommand(ExportProducts::getDefaultName(), ExportProducts::class);

// From Winter v1.3: use a unique key for each command
$this->registerConsoleCommand('acme.import-products', ImportProducts::class);
$this->registerConsoleCommand('acme.export-products', ExportProducts::class);
```

The `getDefaultName()` method is deprecated and will be removed in Symfony 8, so avoid it even if you add the `#[AsCommand]` attribute to your commands.

### The `-s` shortcut for `--silent`

Symfony Console 7.2 added a global `--silent` option to all commands. The `-s` shortcut for the `--silent` option of the `mix:compile`, `mix:create`, `mix:install`, `mix:watch`, `npm:install`, `npm:run`, `npm:update`, `npm:version`, `vite:compile`, `vite:create`, `vite:install` and `vite:watch` commands has therefore been removed. Use `--silent` instead, for example in your deployment scripts.

### Command signatures

Laravel now parses command signatures more strictly. An option written as `{?--option}` is no longer recognized as an option and makes the command fail. Write options as `{--option}` instead.

If your command handles signals by implementing Symfony's `SignalableCommandInterface`, or overrides the `handleSignal()` method of Winter's `HandlesCleanup` trait, the method must now have the following signature:

```php
public function handleSignal(int $signal, int|false $previousExitCode = 0): int|false
```

## Database

> **Impacts:** Plugin developers, some users.

### Raw expressions

The value returned by `DB::raw()` is an `Expression` object that can no longer be converted to a string. Code that concatenates a raw expression with a string, or casts it to a string, now throws an error. Pass plain strings to the `*Raw()` query methods instead, or call the `getValue()` method on the expression:

```php
$sql = DB::raw('COUNT(*)')->getValue(DB::connection()->getQueryGrammar());
```

### Modifying columns

In Laravel 11, calling `->change()` on a column in a migration resets any column attribute that you do not state again. Winter keeps the `nullable`, `default` and `comment` attributes of the existing column when you do not specify them, so most existing migrations keep working. Other attributes, such as `unsigned`, the character set and collation, `autoIncrement` and generated columns, are still reset. We recommend that you state the full column definition when you modify a column:

```php
$table->integer('votes')->unsigned()->default(1)->comment('The vote count')->change();
```

### Column types

The following changes to the schema builder affect new migrations, but also your existing migrations whenever they run on an empty database, for example on a fresh installation or in your test suite:

- The `float()` column type now takes a single `$precision` argument and creates a double-precision column when no precision is given. The `double()` column type no longer accepts a precision and scale. Use `decimal()` for amounts that need a fixed number of decimals.
- The `unsignedDecimal()`, `unsignedDouble()` and `unsignedFloat()` methods have been removed. Use `->unsigned()` on the column instead.
- The spatial column types (`point()`, `lineString()`, `polygon()` and the other spatial types) have been removed. Use `geometry()` or `geography()` instead.

### Limits in eager loads

In Laravel 9, a `limit()` or `take()` inside an eager load constraint limited the total number of related records loaded for all parents together. In Laravel 12, the limit applies to each parent:

```php
// Before Winter v1.3: loads 3 posts in total, spread across the categories
// From Winter v1.3: loads 3 posts for each category
$categories = Category::with(['posts' => function ($query) {
    $query->latest()->limit(3);
}])->get();
```

Check any eager loads that use a limit, as they may now load more records than before. On database servers that support window functions, the limit is applied with a `ROW_NUMBER()` window function. MySQL 9 rejects a random order (`inRandomOrder()`) in that window with error 3587, while MySQL 8.4 accepts it. If you need random related records, load the relation without the limit and use `shuffle()` and `take()` on the collection instead, or query the relation for a single parent.

### MariaDB

Winter now includes a dedicated `mariadb` database driver. The `mysql` driver continues to work with MariaDB servers, so switching is optional.

### Doctrine DBAL

Laravel 11 no longer uses Doctrine DBAL. Winter still requires `doctrine/dbal` and still provides the following methods on its database connections: `getDoctrineConnection()`, `getDoctrineSchemaManager()`, `getDoctrineColumn()`, `registerDoctrineType()`, `isDoctrineAvailable()` and `usingNativeSchemaOperations()`.

The `dbal.types` configuration, `DB::registerDoctrineType()` and the `Schema::getAllTables()`, `Schema::getAllViews()` and `Schema::getAllTypes()` methods have been removed. Use `Schema::getTables()`, `Schema::getViews()` and `Schema::getTypes()` instead. `Schema::getColumnType()` now returns the column type as the database reports it.

### Schema inspection

- On MySQL, MariaDB and PostgreSQL, `Schema::getTables()` now returns the tables of every database or schema that the database user can access, not only the default one. Pass the `schema` argument to limit the results.
- `Schema::getTableListing()` now returns table names prefixed with their schema. Pass `schemaQualified: false` to get the old result.

### Date attributes

The `$dates` property on models is still supported by Winter's `Model` class, so you do not need to change existing models. If you have models that extend Laravel's `Illuminate\Database\Eloquent\Model` class directly, move their `$dates` to the `$casts` property with the `datetime` cast.

## Dates and times

> **Impacts:** Most users, plugin developers.

Carbon has been updated from version 2 to version 3. Carbon is also used for the date attributes of your models, so these changes can affect any code or template that works with dates.

- The `diffInSeconds()`, `diffInMinutes()`, `diffInHours()`, `diffInDays()` and other `diffIn*()` methods now return a float instead of an integer, and the result is negative when the given date is before the date you call the method on. A countdown such as `$expiresAt->diffInDays($now)` now returns a negative number with decimals. To get the old result, pass `true` as the second argument to get an absolute value and convert the result to an integer: `(int) $expiresAt->diffInDays($now, true)`. This also applies to Twig templates, where `{{ post.published_at.diffInDays() }}` now prints decimals.
- `Carbon::createFromTimestamp()` now creates the date in the UTC timezone, instead of in the default timezone of your application. Pass the timezone as the second argument if you need a different one.
- `startOfWeek()` and `endOfWeek()` without an argument now follow the first day of the week of the current locale. For example, with the `en_US` locale the week now starts on Sunday. Pass the day to keep a fixed week start: `startOfWeek(\Carbon\WeekDay::Monday)`.
- The `formatLocalized()`, `setUtf8()`, `setWeekStartsAt()` and `setWeekEndsAt()` methods have been removed. Use `isoFormat()` to format dates in the current locale.
- Comparison methods such as `isSameDay()` now require an argument.

The [Carbon 3 migration guide](https://carbon.nesbot.com/guide/getting-started/migration.html) lists all the changes.

## Helper functions

> **Impacts:** All users, plugin developers.

### `array_first()` and `array_last()`

Winter no longer provides the `array_first()` and `array_last()` helper functions, because PHP 8.5 includes its own functions with the same names. These functions only accept the array: on PHP 8.2 to 8.4 (through the Symfony polyfill), a callback or default value is ignored without any error, and on PHP 8.5 passing one throws an error.

```php
// Before Winter v1.3: returns 2
// From Winter v1.3: returns 1 on PHP 8.4, throws an error on PHP 8.5
$first = array_first([1, 2, 3], fn ($value) => $value > 1);

// Use the Arr class instead: returns 2
$first = Arr::first([1, 2, 3], fn ($value) => $value > 1);
```

Replace every call that passes a callback or a default value with `Arr::first()` or `Arr::last()`.

### Other helpers

- The `$seed` argument of the `array_shuffle()` helper has been removed and is ignored if you pass it.
- `Form::selectMonth()` now formats the month names with the PHP `date()` function instead of `strftime()`, and its default format is `F`. If you pass a format to this method, use the [`date()` format characters](https://www.php.net/manual/en/datetime.format.php) (for example `M` instead of `%b`). The month names are always in English.

## Service container

> **Impacts:** Plugin developers.

When the service container resolves a class, it now uses the default value of a constructor parameter instead of resolving the type of that parameter. A dependency that has a default value of `null` is therefore now `null`. This applies to all classes that are resolved from the container, such as components, controllers and jobs.

```php
// Before Winter v1.3: $users is an instance of the Users class
// From Winter v1.3: $users is null
public function __construct(?CodeBase $cmsObject = null, $properties = [], ?Users $users = null)

// Give the dependency no default value so that it is always resolved
public function __construct(Users $users, ?CodeBase $cmsObject = null, $properties = [])
```

## Authentication

> **Impacts:** Plugin developers.

- Classes that implement Laravel's `Authenticatable` contract must now have a `getAuthPasswordName()` method, which returns the name of the password attribute. Winter's `Winter\Storm\Auth\Models\User` class already provides this method.
- Classes that implement Laravel's `UserProvider` contract must now have a `rehashPasswordIfRequired()` method.
- If you extend `Winter\Storm\Auth\Manager` and override the `setUser()` method, the method must now return `static`.

## Mail

> **Impacts:** Plugin developers that use test cases.

The `Mail::fake()` method now requires the mail manager as an argument:

```php
Mail::fake(app('mail.manager'));
```

## Localization

> **Impacts:** Plugin developers.

- Translation strings of plugins and modules can now also be overridden in the Laravel style, with a `lang/vendor/{namespace}/{locale}/{group}.php` file in the root folder of your project, in addition to the Winter style (`lang/{locale}/{vendor}/{plugin}/{group}.php`).
- `Lang::choice()` now falls back to the fallback locale when a key is missing in the current locale.
- If you extend Winter's translation classes, the `FileLoader::loadPath()` method has been replaced with `loadPaths(array $paths, ...)`, and `Translator::localeForChoice()` now receives the translation key as its first argument.

## Logging

> **Impacts:** Users with custom log handlers, processors or formatters.

Monolog has been updated to version 3. Log records are now `Monolog\LogRecord` objects instead of arrays, and log levels are an enum. If you have custom log handlers, processors, formatters or `tap` classes, update them according to the [Monolog 3 upgrade notes](https://github.com/Seldaek/monolog/blob/main/UPGRADE.md).

## Requests

> **Impacts:** Plugin developers.

In Symfony 7, the `getInt()`, `getBoolean()` and `filter()` methods of a request's parameter bags (for example `$request->query->getInt('page')`) throw an exception for a value that cannot be converted, which results in a "400 Bad Request" response. The `Input::get()` method and Laravel's `$request->integer()` and `$request->boolean()` methods are not affected.

## Laravel packages

> **Impacts:** Plugin developers.

The version of Laravel has been changed from 9.x to 12.x. If you are using packages made for Laravel, you may have to go through and update them to a version compatible with Laravel 12.x.

- The facades that Laravel added after version 9, such as `Context`, `Number`, `Process`, `Schedule` and `Uri`, do not have a global alias in Winter. Import them with their full class name, for example `use Illuminate\Support\Facades\Process;`.
- If a plugin requires the `spatie/once` package, remove it: Laravel now includes its own `once()` function, which conflicts with the one from this package.

## Unit testing

> **Impacts:** Plugin developers that use test cases.

Winter now uses PHPUnit 11. The base test case classes (`\System\Tests\Bootstrap\TestCase` and `\System\Tests\Bootstrap\PluginTestCase`) are still the same, but PHPUnit 11 has changed the way tests are written in several ways.

### Test configuration

The format of the `phpunit.xml` file has changed. Run the following command in the folder of each `phpunit.xml` file to convert it to the new format:

```bash
../../../vendor/bin/phpunit --migrate-configuration
```

The command replaces the `<coverage>` section with a `<source>` section and removes the settings that no longer exist, such as `convertErrorsToExceptions`. It also renames `backupStaticAttributes` to `backupStaticProperties`.

### Writing tests

- Data provider methods must be `public static`.
- Test metadata in docblock comments, such as `@test`, `@dataProvider` and `@depends`, is deprecated and will no longer work in PHPUnit 12. Use attributes instead, such as `#[Test]`, `#[DataProvider('provideValues')]` and `#[Depends('testCreate')]` from the `PHPUnit\Framework\Attributes` namespace. When a test method has at least one attribute, PHPUnit ignores all of its docblock metadata, so convert all of the metadata of a method at once.
- Most methods of PHPUnit's `TestCase` class are now `final`, and a test class that declares a method with the same name, even a private one, causes a fatal error. Rename helper methods in your tests that are called, for example, `status()`, `name()`, `size()`, `groups()`, `result()`, `output()`, `count()`, `run()`, `provides()`, `requires()`, `any()`, `once()`, `never()` or `setLocale()`.
- The `withConsecutive()`, `setMethods()` and `at()` mock methods have been removed. Use `onlyMethods()` instead of `setMethods()`. The `getMockForAbstractClass()`, `getMockForTrait()`, `getObjectForTrait()` and `returnValue()` methods are deprecated.
- Laravel's `expectsEvents()`, `expectsJobs()`, `expectsNotifications()`, `withoutEvents()` and `withoutJobs()` test methods have been removed. Use `Event::fake()`, `Bus::fake()` and `Notification::fake()` instead.
- If your test case has a `tearDown()` method, make sure that it calls `parent::tearDown()`.
- Winter's base `TestCase` class still provides the `assertFileNotExists()`, `assertRegExp()` and `assertObjectHasAttribute()` assertions, but you should switch to `assertFileDoesNotExist()`, `assertMatchesRegularExpression()` and `assertObjectHasProperty()`.

### Running tests

The `winter:test` command keeps its options. Running it with `-m` or `-p` but no module or plugin name now runs the tests of all modules or all plugins. Pass any other PHPUnit arguments after `--`, for example `php artisan winter:test -p Acme.Blog -- --stop-on-failure`.

## Storm library internals

> **Impacts:** Plugin developers that extend Storm classes.

Some methods of the Storm library have new signatures to stay compatible with Laravel 12. If your plugin extends one of the following classes and overrides the method, update the method signature to match:

- `Winter\Storm\Auth\Manager::setUser(Authenticatable $user): static`
- `Winter\Storm\Console\Traits\HandlesCleanup::handleSignal(int $signal, int|false $previousExitCode = 0): int|false`
- `Winter\Storm\Database\Builder::paginate($perPage = null, $currentPage = null, $columns = ['*'], $pageName = 'page', $total = null)`: the new `$total` argument allows you to skip the count query.
- `Winter\Storm\Database\Model::hasAttribute($key)`: Winter's own version has been removed in favor of Laravel's.
- `Winter\Storm\Database\Relations\Concerns\DeferOneOrMany::getWithDeferredQualifiedKeyName(): string`
- `Winter\Storm\Halcyon\MemoryCacheManager::repository(Store $store, array $config = [])`
- `Winter\Storm\Translation\FileLoader::loadPaths(array $paths, $locale, $group)` replaces `loadPath()`.
- `Winter\Storm\Translation\Translator::localeForChoice($key, $locale)`
- The `Winter\Storm\Config\Repository::load()`, `afterLoading()` and `callAfterLoad()` methods now have a `string` type for the namespace.

The database connection classes have also changed. Winter's MySQL, PostgreSQL, SQLite and SQL Server connections now extend Laravel's connection classes directly and share their Winter functionality through the `Winter\Storm\Database\Connections\HasConnection` trait. The old `Winter\Storm\Database\Connections\Connection` base class is deprecated. If you have a custom database connection, grammar or schema blueprint:

- Laravel's `Illuminate\Database\PDO` classes have been removed. Winter provides replacements in the `Winter\Storm\Database\PDO` namespace.
- Query and schema grammars now receive the database connection in their constructor, and `Grammar::setConnection()` and `Connection::withTablePrefix()` have been removed.
- The `Blueprint` class now receives the database connection as its first constructor argument.

## Upgrade guides for dependencies

> **Impacts:** Informational only.

- [PHP 8.2](https://www.php.net/manual/en/migration82.php)
- [Laravel 10](https://laravel.com/docs/10.x/upgrade)
- [Laravel 11](https://laravel.com/docs/11.x/upgrade)
- [Laravel 12](https://laravel.com/docs/12.x/upgrade)
- [Symfony v7](https://github.com/symfony/symfony/blob/7.0/UPGRADE-7.0.md)
- [Carbon 3](https://carbon.nesbot.com/guide/getting-started/migration.html)
- [Monolog 3](https://github.com/Seldaek/monolog/blob/main/UPGRADE.md)
- [Doctrine DBAL 3](https://github.com/doctrine/dbal/blob/3.0.x/UPGRADE.md)
- [PHPUnit 10](https://github.com/sebastianbergmann/phpunit/blob/10.0.0/ChangeLog-10.0.md) & [PHPUnit 11](https://github.com/sebastianbergmann/phpunit/blob/11.0.0/ChangeLog-11.0.md)
