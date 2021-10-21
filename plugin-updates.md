# Version History

- [Introduction](#introduction)
- [Update process](#update-process)
    - [Plugin dependencies](#plugin-dependencies)
- [Plugin version file](#version-file)
    - [Important updates](#important-updates)
    - [Migration and seed files](#migration-seed-files)

<a name="introduction"></a>
## Introduction

It is good practice for plugins to maintain a change log that documents any changes or improvements in the code. In addition to writing notes about changes, this process has the useful ability to execute [migration and seed files](../database/structure) in their correct order.

The change log is stored in a YAML file called `version.yaml` inside the **/updates** directory of a plugin, which co-exists with migration and seed files. This example displays a typical plugin updates directory structure:

```css
📂 plugins
 ┣ 📂 myauthor                              <-- Author name
 ┃ ┣ 📂 myplugin                            <-- Plugin name
 ┃ ┃ ┣ 📂 updates                           <-- Database migrations
 ┃ ┃ ┃ ┃ ┣ 📂 v1.0.0                        <-- Migrations for a specific version of the plugin
 ┃ ┃ ┃ ┃ ┃ ┣ 📜 seed_the_database.php       <-- Database seed file, referenced in version.yaml
 ┃ ┃ ┃ ┃ ┃ ┗ 📜 create_records_table.php    <-- Database migration file, referenced in version.yaml
 ┃ ┃ ┃ ┗ 📜 version.yaml                    <-- Changelog
 ```

<a name="update-process"></a>
## Update process

During an update the system will notify the user about recent changes to plugins, it can also prompt them about [important or breaking changes](#important-updates). Any given migration or seed file will only be excuted once after a successful update. Winter executes the update process automatically when any of the following occurs:

1. When an administrator signs in to the backend.
1. When the system is updated using the update feature in the backend area.
1. When the [console command](../console/commands#console-up-command) `php artisan winter:up` is called in the command line from the application directory.

> **NOTE:** The plugin [initialization process](../plugin/registration#routing-initialization) is disabled during the update process, this should be a consideration in migration and seeding scripts.

<a name="plugin-dependencies"></a>
### Plugin dependencies

Updates are applied in a specific order, based on the [defined dependencies in the plugin registration file](../plugin/registration#dependency-definitions). Plugins that are dependant will not be updated until all their dependencies have been updated first.

```php
<?php namespace Acme\Blog;

class Plugin extends \System\Classes\PluginBase
{
    public $require = ['Acme.User'];
}
```

In the example above the **Acme.Blog** plugin will not be updated until the **Acme.User** plugin has been fully updated.

<a name="version-file"></a>
## Plugin version file

The **version.yaml** file, called the *Plugin version file*, contains the version comments and refers to database scripts in the correct order. Please read the [Database structure](../database/structure) article for information about the migration files. This file is required if you're going to submit the plugin to the [Marketplace](https://wintercms.com/marketplace). Here is an example of a plugin version file:

```yaml
"v1.0.1": "First version"
"v1.0.2": "Second version"
"v1.0.3":
    - "Third version"
    - "which has a lot of changes"
    - "including this one"
"v1.1.0": "!!! Important update"
"v1.1.1":
    - "Update with a migration and seed"
    - "and here's the migration"
    - "v1.1.1/create_tables.php"
    - "and here's the seed"
    - "v1.1.1/seed_the_database.php"
```

> **NOTE:** `version.yaml` files support having multiple text entries per version as the change log description. You can have as many update messages as you want, migration files can be listed in any position too.

As you can see above, there should be a key that represents the version number followed by the update message, which is either a string or an array containing update messages. For updates that refer to migration or seeding files, lines that are script file names can be placed in any position. An example of a comment with no associated update files:

```yaml
"v1.0.1": "A single comment that uses no update scripts."
```

<a name="important-updates"></a>
### Important updates

Sometimes a plugin needs to introduce features that will break websites already using the plugin. If an update comment in the **version.yaml** file begins with three exclamation marks (`!!!`) then it will be considered *Important* and will require the user to confirm before updating. An example of an important update comment:

```yaml
"v1.1.0": "!!! This is an important update that contains breaking changes."
```

When the system detects an important update it will provide three options to proceed:

1. Confirm update
1. Skip this plugin (once only)
1. Skip this plugin (always)

Confirming the comment will update the plugin as usual, or if the comment is skipped it will not be updated.

<a name="migration-seed-files"></a>
### Migration and seed files

As previously described, updates also define when [migration and seed files](../database/structure) should be applied. An update line with a comment and updates:

```yaml
"v1.1.1":
    - "This update will execute the two scripts below."
    - "v1.1.1/some_upgrade_file.php"
    - "v1.1.1/some_seeding_file.php"
```

The update file name should use *snake_case* while the containing PHP class should use *CamelCase*. For a file named **some_upgrade_file.php** the corresponding class would be `SomeUpgradeFile`.

```php
<?php namespace Acme\Blog\Updates;

use Schema;
use Winter\Storm\Database\Updates\Migration;

/**
 * some_upgrade_file.php
 */
class SomeUpgradeFile extends Migration
{
    ///
}
```
