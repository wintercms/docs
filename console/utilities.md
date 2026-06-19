# Utility Commands

The following commands are utility commands available on Winter installations.

## Run unit tests

```bash
php artisan winter:test [--core] [--plugin=] [--configuration=]
```

The `winter:test` command runs the unit tests for the entire project, a specific plugin, or the Winter core.

To run the entire project's unit tests:

```bash
php artisan winter:test
```

Or, to run only the core unit tests, use the `-o` or `--core` option:

```bash
php artisan winter:test -o
```

To run a specific plugin's tests, use the `-p` or `--plugin=` option:

```bash
php artisan winter:test -p Acme.Demo
```

To run a custom test suite, use the `-c` or `--configuration=` option:

```bash
php artisan winter:test -c ./custom-path/phpunit.xml
```

If using additional PHPUnit parameters / options, they must be included after the winter:test command's options:

```bash
php artisan winter:test -p Acme.Demo --filter=FilteredTest --stop-on-failure
```

## Code style check

The `winter:sniff` command allows you to check source code against the [Winter CMS code style guidelines](../architecture/developer-guide#php-coding-standards) as set forth in the [Developer Guide](../architecture/developer-guide) to ensure consistent code formatting. It uses the [PHP_CodeSniffer](https://github.com/PHPCSStandards/PHP_CodeSniffer/) library under the hood.

```bash
php artisan winter:sniff [--plugin=] [--configuration=]
```

By default, this code style check will run in all core files. To test a plugin, you may use the `-p` or `--plugin=` option and provide the plugin code:

```bash
php artisan winter:sniff -p Acme.Demo
```

If the plugin or core files do not contain a `phpcs.xml` configuration file for the code checker, you will be prompted to create one automatically.

If you wish to use a custom configuration for PHP_CodeSniffer, you can specify the path with the `-c` or `--configuration=` option:

```bash
php artisan winter:sniff -c ./custom-path/phpcs.xml
```

By default, warnings and errors will both be shown if detected in any source code. You can suppress warnings by using the `-e` or `--no-warnings` option.

```bash
php artisan winter:sniff -e
```

If you wish to show only a summary (a list of files with a count of warnings and/or errors), you may use the `-s` or `--summary` option.

```bash
php artisan winter:sniff -s
```

## Utility runner

`winter:util` - a generic command to perform general utility tasks, such as cleaning up files or combining files. The arguments passed to this command will determine the task used.

### Compile Winter assets

Outputs combined system files for JavaScript (js), StyleSheets (less), client side language (lang), or everything (assets).

```bash
php artisan winter:util compile assets
php artisan winter:util compile lang
php artisan winter:util compile js
php artisan winter:util compile less
```

To combine without minification, pass the `--debug` option.

```bash
php artisan winter:util compile js --debug
```

### Pull all repos

This will execute the command `git pull` on all theme and plugin directories.

```bash
php artisan winter:util git pull
```

### Purge thumbnails

Deletes all generated thumbnails in the uploads directory.

```bash
php artisan winter:util purge thumbs
```

### Purge uploads

Deletes files in the uploads directory that do not exist in the "system_files" table.

```bash
php artisan winter:util purge uploads
```

### Purge orphaned uploads

Deletes records in "system_files" table that do not belong to any other model.

```bash
php artisan winter:util purge orphans
```

To also delete records that have no associated file in the local storage, pass the `--missing-files` option.

```bash
php artisan winter:util purge orphans --missing-files
```
