# Plugin Replacement & Forking

- [Registering a plugin as a replacement](#replace-registration)
    - [Version constraints](#version-constraints)
- [Aliasing](#aliasing)
    - [Configuration](#aliases-config)
    - [Language & Translations](#aliases-lang)
    - [Settings](#aliases-settings)
    - [Navigation](#aliases-navigation)
- [Handling migrations, seeders and table references](#plugin-replace-data)

Plugin replacement is a feature that allows you to create a plugin that replaces (or overrides) another plugin. This is useful when you're forking a plugin to add your own functionality but want to be able to seamlessly migrate from and act as a drop in replacement for the original plugin (i.e. retaining original data, fulfilling other plugin's dependencies on the original plugin, etc).

<a name="plugin-replace-registration"></a>
## Registering a plugin as a replacement

To enable the plugin replacement feature, specify the identifier for the plugin that your plugin is replacing in the `replaces` key within the `pluginDetails` array, along with the version constraints that define what versions of the plugin are able to be replaced by your plugin.

```php
public function pluginDetails()
{
    return [
        'name'     => 'Acme Plugin',
        'replaces' => [
            'Acme.Original' => '>=5.0 <=6.0.4'
        ],
    ];
}
```

<a name="version-constraints"></a>
### Version constraints

Version constraints allow you to restrict your plugin to only override currently installed plugins of specific versions. The above example showcases only replacing a plugin if the original plugin is any version between `5.0` and `6.0.4` inclusive. Most of the time the actual version constraint you'll use will be much simpler, a simple `<2.0` to indicate all versions immediately prior to the version you first release your replacement plugin as.

This means you don't have to worry about new versions of the original plugin having changes that may conflict with your changes to the plugin.

Version constraints are specified in the [same format that Composer uses](https://getcomposer.org/doc/articles/versions.md#writing-version-constraints). Some valid examples would be:

- `1.0`
- `>=1.0.3`
- `<2.0`
- `>=1.5.0 <2.0.0`
- `self.version`

By specifying a version, your plugin will check what version the original plugin is installed at and only if it's version matches the constraint will it disable the original and enable the replacement. If this match fails, then the replacement will be disabled and the original plugin will stay enabled.

<a name="aliases"></a>
## Aliasing

> **NOTE:** This is for reference only. By registering a plugin as a replacement in the Plugin registration file, Winter automatically handles registering these aliases for you.

Aliasing is a feature of Winter that allows for backwards compatibility and support for inheriting replaced plugins:

- Configuration options
- Languages & translations
- Settings
- Navigation

This will allow, for example, plugins that use the original plugin functionality to still work even if the original class names are used. If a plugin replaces an original plugin that is a dependency of a third plugin, aliasing will resolve the dependency for the third plugin, allowing the third plugin to continue to function.

<a name="aliases-config"></a>
### Configuration

Configuration supports 2 different types of aliasing: `registerNamespaceAlias` & `registerPackageFallback`.

##### registerNamespaceAlias

This method allows for redirection of the alias to the namespace while accessing config values.

```php
Config::registerNamespaceAlias('winter.replacement', 'winter.original');
```

For example, register the following config as `plugins/winter/replacement/config/config.php`:

```php
<?php

return [
    'foo' => 'bar'
];
```

The config will be accessible via the alias registered:

```php
config('winter.original::foo'); // returns bar
```

##### registerPackageFallback

This method allows falling back to an aliased global config (a config specified in `/config/acme/plugin/config.php`).

```php
Config::registerPackageFallback('winter.replacement', 'winter.original');
```

The logic to this is as follows:

- If `/config/winter/replacement/config.php` exists it will be registered under the `winter.replacement` namespace.
- If `/config/winter/replacement/config.php` does not exist, it will check `/config/winter/original/config.php` and if found,
  it will be registered under the `winter.replacement`.

<a name="aliases-lang"></a>
### Languages & translation

Allows for redirection of calls to the alias and returns values from the namespace.

```php
Lang::registerNamespaceAlias('winter.replacement', 'winter.original');
```

For example, register the following config as `plugins/winter/replacement/lang/en/lang.php`:

```php
<?php

return [
    'foo' => 'bar'
];
```

The translation will be accessible via the alias registered:

```php
Lang::get('winter.original::foo'); // returns bar
```

<a name="aliases-settings"></a>
### Settings

There are 2 methods for registering settings aliases. Firstly the aliases can be registered prior to the `PluginManager` init via `lazyRegisterOwnerAlias`.

```php
SettingsManager::lazyRegisterOwnerAlias('Winter.Replacement', 'Winter.Original');
```

If the `PluginManager` has been loaded, then aliases can be registered via:

```php
SettingsManager::instance()->registerOwnerAlias('Winter.Replacement', 'Winter.Original');
```

<a name="aliases-navigation"></a>
### Navigation

There are 2 methods for registering settings aliases. Firstly the aliases can be registered prior to the `PluginManager` init via `lazyRegisterOwnerAlias`.

```php
NavigationManager::lazyRegisterOwnerAlias('Winter.Replacement', 'Winter.Original');
```

If the `PluginManager` has been loaded, then aliases can be registered via:

```php
NavigationManager::instance()->registerOwnerAlias('Winter.Replacement', 'Winter.Original');
```

<a name="plugin-replace-data"></a>
## Handling migrations, seeders and table references

When forking a plugin and using the replace functionality, you will need to handle migrating the data from the original plugin to your replacing plugin via migrations, seeders and the model classes. To do this we recommend the following:

- Create a migration to rename tables
- Update models to reference your new table
- Check migrations for any usage of models

### Table renaming

An example migration could look something like this:

```php
<?php namespace Winter\Plugin\Updates;

use Schema;
use Winter\Storm\Database\Updates\Migration;

class RenameTables extends Migration
{
    const TABLES = [
        'example',
        'foo',
        'bar'
    ];

    public function up()
    {
        foreach (self::TABLES as $table) {
            $from = 'acme_plugin_' . $table;
            $to = 'winter_plugin_' . $table;

            if (Schema::hasTable($from) && !Schema::hasTable($to)) {
                Schema::rename($from, $to);
            }
        }
    }

    public function down()
    {
        foreach (self::TABLES as $table) {
            $from = 'winter_plugin_' . $table;
            $to = 'acme_plugin_' . $table;

            if (Schema::hasTable($from) && !Schema::hasTable($to)) {
                Schema::rename($from, $to);
            }
        }
    }
}
```

### Migrations using models

If an old migration (i.e. any migration that runs before the migration that renames the tables) is using a model to populate data, it will be referencing the new table and that will cause issues while updating. The solution to this is dynamically renaming the table before inserting/modifying data:

```php
ExampleModel::extend(function ($model) {
    $model->setTable('acme_plugin_example');
});

// execute seeding code

ExampleModel::extend(function ($model) {
    $model->setTable('winter_plugin_example');
});
```

If the models use the `unique` validation rule, you should make sure that the rule is implemented without any modifiers (i.e. just `'slug' => 'unique'`, not `'slug' => 'unique:winter_plugin_table'` so that the call to `$model->setTable()` will also take effect in that validation logic.
