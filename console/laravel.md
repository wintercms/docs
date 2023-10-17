# Laravel Utility Commands

The following commands are utility commands provided by Laravel available on Winter installations.

## Clear the cache

```bash
php artisan cache:clear
```

The `cache:clear` command flushes the entire application cache that is used to increase the performance of Winter. We routinely cache compiled template files and system data, however, in rare cases this cached data can result in your project showing outdated data or not reflecting more recent changes. This command will delete the cache and ensure the latest data is available.

cache:forget {key : The key to remove} {store? : The store to remove the key from}
Remove an item from the cache
