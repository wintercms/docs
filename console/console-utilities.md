# Utility Commands

- [Run unit tests](#winter-test)
- [Utility runner](#winter-util)
  - [Compile Winter assets](#winter-util-compile-assets)
  - [Git pull](#winter-util-git-pull)
  - [Purge thumbnails](#winter-util-purge-thumbs)
  - [Purge uploads](#winter-util-purge-uploads)
  - [Purge orphaned uploads](#winter-util-purge-orphans)

The following commands are utility commands available on Winter installations.

<a name="winter-test"></a>
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

<a name="winter-util"></a>
## Utility runner

`winter:util` - a generic command to perform general utility tasks, such as cleaning up files or combining files. The arguments passed to this command will determine the task used.

<a name="winter-util-compile-assets"></a>
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

<a name="winter-util-git-pull"></a>
### Pull all repos

This will execute the command `git pull` on all theme and plugin directories.

```bash
php artisan winter:util git pull
```

<a name="winter-util-purge-thumbs"></a>
### Purge thumbnails

Deletes all generated thumbnails in the uploads directory.

```bash
php artisan winter:util purge thumbs
```

<a name="winter-util-purge-uploads"></a>
### Purge uploads

Deletes files in the uploads directory that do not exist in the "system_files" table.

```bash
php artisan winter:util purge uploads
```

<a name="winter-util-purge-orphans"></a>
### Purge orphaned uploads

Deletes records in "system_files" table that do not belong to any other model.

```bash
php artisan winter:util purge orphans
```

To also delete records that have no associated file in the local storage, pass the `--missing-files` option.

```bash
php artisan winter:util purge orphans --missing-files
```
