---
title: "Getting Started: Installation"
description: "Documentation on the different ways to install Winter CMS for your next project."
---
# Installation

There are two ways you can install Winter, either using the [Web-based installer](#web-based-installation) or [Composer installation](../architecture/using-composer) instructions. Before you proceed, you should check that your server meets the minimum system requirements.

## Minimum system requirements

Winter CMS has some server requirements for web hosting:

- PHP version 8.0 or above. (we recommend at least PHP 8.1)
- The following PHP extensions installed and enabled:
    - cURL
    - GD
    - Mbstring
    - OpenSSL
    - PDO (including the drivers of your database server)
    - SimpleXML
    - ZipArchive

We also recommend the installation of the PDO SQLite extension, regardless of your database type, as some functions in Winter CMS may benefit from being able to use temporary SQLite databases.

### Supported Databases

- MariaDB 10.2+ ([Version Policy](https://mariadb.org/about/#maintenance-policy))
- MySQL 5.7+ ([Version Policy](https://en.wikipedia.org/wiki/MySQL#Release_history))
- PostgreSQL 9.6+ ([Version Policy](https://www.postgresql.org/support/versioning/))
- SQLite 3.8.8+
- SQL Server 2017+ ([Version Policy](https://docs.microsoft.com/en-us/lifecycle/products/?products=sql-server))

When using the SQL Server database engine, you will need to install the [group concatenation](https://github.com/orlando-colamatteo/ms-sql-server-group-concat-sqlclr) user-defined aggregate.

## Web-based installation

The [Web Installer](https://github.com/wintercms/web-installer) is the recommended way to install Winter for **non-technical users**. It is simpler than the command-line installation and doesn't require any special skills.

> **NOTE:** If you are a developer, we recommend that you [install via Composer instead](../architecture/using-composer)

1. Prepare an empty directory on the web server that will host your Winter CMS installation. It can be a main domain, sub-domain or subfolder.
2. [Download the "install.zip" file](https://github.com/wintercms/web-installer/releases/latest/download/install.zip) from the latest release of the Winter CMS Web Installer into this folder.
3. Unzip the downloaded ZIP file.
4. Grant write permissions to all files and folders that were extracted.
5. In your web browser, navigate to the URL pointing to that folder, and include `/install.html` at the end of the URL.
6. Follow the instructions given in the installer.

![Winter CMS Installer](https://raw.githubusercontent.com/wintercms/docs/develop/images/web-installer.jpg)

### Troubleshooting a web-based installation

1. **Unable to connect to the Winter Marketplace API**: If your server has a firewall blocking requests to port 443, you will need to allow requests and responses for this port. Contact your system administrator to allow access to this port.

2. **Installer fails on the "Determining dependencies" or "Installing dependencies" step**: Under the hood, the web installer uses Composer to process and install the dependencies necessary to run Winter CMS - note, you *do not* need Composer installed as a CLI tool for this to work. This process may require a larger amount of memory to complete - if your environment restricts memory usage for your applications, consider allowing up to 1.5GB of memory temporarily for the installer, then reduce it after the installation is complete. The installer will try to do this automatically.

3. **Installer does not display or function correctly**: The web installer has been built on modern frontend frameworks, and may require the use of a more recent browser version. Consider installing Mozilla Firefox, Microsoft Edge or Google Chrome and keeping it up-to-date.

4. **Unable to parse JSON response**: Depending on your internet connection, downloading all the source files may take more time than the [`max_execution_time` PHP configuration value](https://www.php.net/manual/en/info.configuration.php#ini.max-execution-time) allows; causing the download to end with an incomplete file base. Modify the PHP configuration to increase this value and try again.

5. **Unable to determine dependencies for Winter CMS. Your composer.json file is invalid.**: This error has been reported by people using shared hosting, and is usually the result of either the `proc_*` methods being listed in the `disable_functions` PHP INI setting, or the `pcntl` extension for PHP not being enabled. Unfortunately, at this stage, there is no workaround, but you may be able to use the Installer on a local development environment that does not have these limitations, and then simply copy the full directory to your shared hosting once completed. We are actively looking into more long-term fix for this behaviour.

## Command-line installation

If you feel more comfortable with a command-line or want to use Composer, there is a CLI install process on the [Using Composer page](../architecture/using-composer).

## Post-installation steps

Once your Winter CMS installation is complete, there are a couple of post-installation steps that we recommend that you review before proceeding.

### Delete installation files

If you have used the [Web-based installation](#web-based-installation) method, you should verify that the installation files have been deleted. The installer will attempt to cleanup after itself, but in rare circumstances, it may be unable to do so.

Please ensure that the following directory and files have been removed:

```treeview
MyWinterFolder/
|-- install/       # Installation directory
`-- install.html   # Installation script
```

### Review configuration

Configuration files are stored in the `config` directory of the application. While each file contains descriptions for each setting, it is important to review the [common configuration options](../setup/configuration) to ensure that they are suitable for your circumstances.

For example, in production environments you may wish to enable [CSRF protection](../setup/configuration#csrf-protection), while in development environments, you may want to enable [bleeding edge updates](../setup/configuration#bleeding-edge-updates).

In particular, we strongly recommend disabling [debug mode](../setup/configuration#debug-mode) for production environments. You may also want to use a [public folder](../setup/configuration#using-a-public-folder) for additional security.

### Setting up the scheduler

If you intend to use scheduled tasks, or install plugins that use scheduled tasks to function, you should add the following cron entry to your server. Editing the crontab is commonly performed with the command `crontab -e` in the command-line interface of your server.

```
* * * * * php /path/to/artisan schedule:run &> /dev/null
```

Be sure to replace `/path/to/artisan` with the absolute path to the `artisan` file in the root directory of your Winter installation. This cron task will call the command scheduler every minute, to which Winter will evaluate any scheduled tasks and run the tasks that are due for execution.

> **NOTE**: If you are adding this to the system crontab (`/etc/cron.d`), you'll need to specify the user to run the command as immediately after `* * * * *`.

### Setting up queue workers

You may optionally set up an external queue for processing queued jobs. By default, Winter will run queued jobs asynchronously, which may cause slower performance and response times for users if the jobs are large. This behavior can be changed by setting the `default` parameter in the `config/queue.php`. Please review the [Queue service](../services/queues.md) documentation for more information on setting up a queue runner.

If you decide to use the `database` queue driver, it is a good idea to add a crontab entry for the command `php artisan queue:work --once` to process the first available job in the queue.
