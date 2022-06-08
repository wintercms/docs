# Theme Management Commands

- [Download and install a theme for Winter](#theme-install)
- [List installed themes](#theme-list)
- [Switch theme](#theme-use)
- [Remove a theme](#theme-remove)
- [Synchronise database templates](#theme-sync)

The following commands are used for managing themes within your Winter installation.

<a name="theme-install"></a>
## Download and install a theme for Winter

```bash
php artisan theme:install <theme code> [directory]
```

The `theme:install` command downloads and installs the theme by its theme code in the format **AuthorName.ThemeName**. You can retrieve the theme code through the Winter marketplace.

By default, the theme will be installed in the `themes` folder, in a subdirectory `authorname-themename`. You can customise the subdirectory name by specifying the optional `directory` argument.

<a name="theme-list"></a>
## List installed themes

```bash
php artisan theme:list
```

The `theme:list` command will present a list of themes installed in the Winter installation. It will also display whether the theme is active or not besides each theme item.

<a name="theme-use"></a>
## Switch theme

```bash
php artisan theme:use <theme code>
```

The `theme:use` command allows you to switch to a specific theme for your Winter installation. This theme will then be used for the public pages on your project.

<a name="theme-remove"></a>
## Remove a theme

```bash
php artisan theme:remove <theme code>
```

The `theme:remove` command allows you remove a theme installed on your Winter CMS installation. This will remove the files for theme. **This is a destructive action.** You will prompted to confirm the action before proceeding.

<a name="theme-sync"></a>
## Synchronise database templates

```bash
php artisan theme:sync [theme code] [--target=] [--force] [--paths=]
```

The `theme:sync` command synchronises a theme's content between the filesystem and database when the [Database Templates](../cms/themes#database-driven-themes) feature is enabled.

By default the theme that will be synchronised is the currently active theme. You can specify any theme to sync by passing the desired theme's code as the `theme code` argument.

```bash
php artisan theme:sync my-custom-theme
```

By default, the sync direction will be from the database to the filesytem (i.e. you're syncing changes on a remote host to the filesystem for tracking in a version control system). However, you can change the direction of the sync by specifying `--target=database`. This is useful if you have changed the underlying files that make up the theme and you want to force the site to pick up your changes even if they have made changes of their own that are stored in the database.

```bash
php artisan theme:sync --target=database
```

By default the command requires user interaction to confirm that they want to complete the sync (including information about the amount of paths affected, the theme targeted, and the target & source of the sync). To override the need for user interaction (i.e. if running this command in a deploy / build script of some sort) just pass the `--force` option:

```bash
php artisan theme:sync --force
```

Unless otherwise specified, the command will sync all the valid paths (determined by the Halcyon model instances returned to the `system.console.theme.sync.getAvailableModelClasses` event) available in the theme. To manually specify specific paths to be synced pass a comma separated list of paths to the `--paths` option:

```bash
php artisan theme:sync --paths=partials/header.htm,content/contact.md
```