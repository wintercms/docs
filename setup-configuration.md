# App Configuration

- [Webserver configuration](#webserver-configuration)
    - [Apache configuration](#apache-configuration)
    - [Nginx configuration](#nginx-configuration)
    - [Lighttpd configuration](#lighttpd-configuration)
    - [IIS configuration](#iis-configuration)
- [Application configuration](#app-configuration)
    - [Debug mode](#debug-mode)
    - [Safe mode](#safe-mode)
    - [CSRF protection](#csrf-protection)
    - [Bleeding edge updates](#edge-updates)
    - [Using a public folder](#public-folder)
    - [Using a shared hosting provider](#shared-hosting)
    - [Backend-only Mode](#backend-only-mode)
    - [Trusted Hosts](#trusted-hosts)
    - [Trusted Proxies](#trusted-proxies)
- [Environment configuration](#environment-config)
    - [Defining a base environment](#base-environment)
    - [Domain driven environment](#domain-environment)
    - [Converting to DotEnv configuration](#dotenv-configuration)

All of the configuration files for Winter are stored in the `config/` directory. Options are usually documented in the configuration files directly, so it is recommended to look through the files to become familiar with the available options.

<a name="webserver-configuration"></a>
## Web server configuration

Winter has basic configuration that should be applied to your webserver. Common webservers and their configuration can be found below.

<a name="apache-configuration"></a>
### Apache configuration

If your webserver is running Apache there are some extra system requirements:

1. mod_rewrite should be installed
1. AllowOverride option should be switched on

In some cases you may need to uncomment this line in the `.htaccess` file:

    ##
    ## You may need to uncomment the following line for some hosting environments,
    ## if you have installed to a subdirectory, enter the name here also.
    ##
    # RewriteBase /

If you have installed to a subdirectory, you should add the name of the subdirectory also:

    RewriteBase /mysubdirectory/

<a name="nginx-configuration"></a>
### Nginx configuration

There are small changes required to configure your site in Nginx.

`nano /etc/nginx/sites-available/default`

Use the following code in **server** section. If you have installed Winter into a subdirectory, replace the first `/` in location directives with the directory Winter was installed under:

    location / {
        # Let Winter CMS handle everything by default.
        # The path not resolved by Winter CMS router will return Winter CMS's 404 page.
        # Everything that does not match with the whitelist below will fall into this.
        rewrite ^/.*$ /index.php last;
    }

    # Pass the PHP scripts to FastCGI server
    location ~ ^/index.php {
        # Write your FPM configuration here

    }

    # Whitelist
    ## Let Winter handle if static file not exists
    location ~ ^/favicon\.ico { try_files $uri /index.php; }
    location ~ ^/sitemap\.xml { try_files $uri /index.php; }
    location ~ ^/robots\.txt { try_files $uri /index.php; }
    location ~ ^/humans\.txt { try_files $uri /index.php; }

    # Block access to all dot files and folders except .well-known
    location ~ /\.(?!well-known).* { deny all; }

    ## Let nginx return 404 if static file not exists
    location ~ ^/storage/app/uploads/public { try_files $uri 404; }
    location ~ ^/storage/app/media { try_files $uri 404; }
    location ~ ^/storage/app/resized { try_files $uri 404; }
    location ~ ^/storage/temp/public { try_files $uri 404; }

    location ~ ^/modules/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/resources { try_files $uri 404; }
    location ~ ^/modules/.*/behaviors/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/behaviors/.*/resources { try_files $uri 404; }
    location ~ ^/modules/.*/widgets/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/widgets/.*/resources { try_files $uri 404; }
    location ~ ^/modules/.*/formwidgets/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/formwidgets/.*/resources { try_files $uri 404; }
    location ~ ^/modules/.*/reportwidgets/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/reportwidgets/.*/resources { try_files $uri 404; }

    location ~ ^/plugins/.*/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/resources { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/behaviors/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/behaviors/.*/resources { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/reportwidgets/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/reportwidgets/.*/resources { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/formwidgets/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/formwidgets/.*/resources { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/widgets/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/widgets/.*/resources { try_files $uri 404; }

    location ~ ^/themes/.*/assets { try_files $uri 404; }
    location ~ ^/themes/.*/resources { try_files $uri 404; }

<a name="lighttpd-configuration"></a>
### Lighttpd configuration

If your webserver is running Lighttpd you can use the following configuration to run Winter CMS. Open your site configuration file with your favorite editor.

`nano /etc/lighttpd/conf-enabled/sites.conf`

Paste the following code in the editor and change the **host address** and  **server.document-root** to match your project.

    $HTTP["host"] =~ "domain.example.com" {
        server.document-root = "/var/www/example/"

        url.rewrite-once = (
            "^/(plugins|modules/(system|backend|cms))/(([\w-]+/)+|/|)assets/([\w-]+/)+[-\w^&'@{}[\],$=!#().%+~/ ]+\.(jpg|jpeg|gif|png|svg|swf|avi|mpg|mpeg|mp3|flv|ico|css|js|woff|ttf)(\?.*|)$" => "$0",
            "^/(system|themes/[\w-]+)/assets/([\w-]+/)+[-\w^&'@{}[\],$=!#().%+~/ ]+\.(jpg|jpeg|gif|png|svg|swf|avi|mpg|mpeg|mp3|flv|ico|css|js|woff|ttf)(\?.*|)$" => "$0",
            "^/storage/app/uploads/public/[\w-]+/.*$" => "$0",
            "^/storage/app/media/.*$" => "$0",
            "^/storage/app/resized/.*$" => "$0",
            "^/storage/temp/public/[\w-]+/.*$" => "$0",
            "^/(favicon\.ico)$" => "$0",
            "(.*)" => "/index.php$1"
        )
    }

<a name="iis-configuration"></a>
### IIS configuration

If your webserver is running Internet Information Services (IIS) you can use the following in your **web.config** configuration file to run Winter CMS.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
                <rule name="Winter CMS to handle all non-whitelisted URLs" stopProcessing="true">
                    <match url="^index.php" negate="true" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
                        <add input="{REQUEST_FILENAME}" pattern="/.well-known/*" ignoreCase="false" negate="true" />
                        <add input="{REQUEST_FILENAME}" pattern="/storage/app/uploads/public/.*" ignoreCase="false" negate="true" />
                        <add input="{REQUEST_FILENAME}" pattern="/storage/app/media/.*" ignoreCase="false" negate="true" />
                        <add input="{REQUEST_FILENAME}" pattern="/storage/app/resized/.*" ignoreCase="false" negate="true" />
                        <add input="{REQUEST_FILENAME}" pattern="/storage/temp/public/.*" ignoreCase="false" negate="true" />
                        <add input="{REQUEST_FILENAME}" pattern="/themes/.*/(assets|resources)/.*" ignoreCase="false" negate="true" />
                        <add input="{REQUEST_FILENAME}" pattern="/plugins/.*/(assets|resources)/.*" ignoreCase="false" negate="true" />
                        <add input="{REQUEST_FILENAME}" pattern="/modules/.*/(assets|resources)/.*" ignoreCase="false" negate="true" />
                    </conditions>
                    <action type="Rewrite" url="index.php" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>
</configuration>
```

<a name="app-configuration"></a>
## Application configuration

<a name="debug-mode"></a>
### Debug mode

The debug setting is found in the `config/app.php` configuration file with the `debug` parameter and is enabled by default.

When enabled, this setting will show detailed error messages when they occur along with other debugging features. While useful during development, debug mode should always be disabled when used in a live production site. This prevents potentially sensitive information from being displayed to the end-user.

The debug mode uses the following features when enabled:

1. [Detailed error pages](../cms/pages#error-page) are displayed.
1. Failed user authentication provides a specific reason.
1. [Combined assets](../markup/filter-theme) are not minified by default.
1. [Safe mode](#safe-mode) is disabled by default.

> **Important**: Always set the `app.debug` setting to `false` for production environments.

<a name="safe-mode"></a>
### Safe mode

The safe mode setting is found in the `config/cms.php` configuration file with the `enableSafeMode` parameter. The default value is `null`.

If safe mode is enabled, the PHP code section is disabled in CMS templates for security reasons. If set to `null`, safe mode is on when [debug mode](#debug-mode) is disabled.

<a name="csrf-protection"></a>
### CSRF protection

Winter provides an easy method of protecting your application from cross-site request forgeries. First a random token is placed in your user's session. Then when a [opening form tag is used](../services/html#form-tokens) the token is added to the page and submitted back with each request.

While CSRF protection is enabled by default, you can disable it with the `enableCsrfProtection` parameter in the `config/cms.php` configuration file.

<a name="edge-updates"></a>
### Bleeding edge updates

The Winter platform and some marketplace plugins will implement changes in two stages to ensure overall stability and integrity of the platform. This means they have a *test build* in addition to the default *stable build*.

You can instruct the platform to prefer test builds from the marketplace by changing the `edgeUpdates` parameter in the `config/cms.php` configuration file.

    /*
    |--------------------------------------------------------------------------
    | Bleeding edge updates
    |--------------------------------------------------------------------------
    |
    | If you are developing with Winter, it is important to have the latest
    | code base, set this value to 'true' to tell the platform to download
    | and use the development copies of core files and plugins.
    |
    */

    'edgeUpdates' => false,

> **NOTE:** For plugin developers we recommend enabling **Test updates** for your plugins listed on the marketplace, via the Plugin Settings page.

> **NOTE:** If using [Composer](../console/commands#console-install-composer) to manage updates, then replace the default Winter CMS requirements in your `composer.json` file with the following in order to download updates directly from the develop branch.

    "winter/storm": "dev-develop as 1.0",
    "winter/wn-system-module": "dev-develop",
    "winter/wn-backend-module": "dev-develop",
    "winter/wn-cms-module": "dev-develop",
    "laravel/framework": "~6.0",

<a name="public-folder"></a>
### Using a public folder

For ultimate security in production environments you may configure your web server to use a **public/** folder to ensure only public files can be accessed. First you will need to spawn a public folder using the `winter:mirror` command.

    php artisan winter:mirror public/

This will create a new directory called **public/** in the project's base directory, from here you should modify the webserver configuration to use this new path as the home directory, also known as *wwwroot*.

> **NOTE**: The above command may need to be performed with System Administrator or *sudo* privileges. It should also be performed after each system update or when a new plugin is installed.

<a name="shared-hosting"></a>
### Using a shared hosting provider

If you share a server with other users, you should act as if your neighbor's site was compromised. Make sure all files with passwords (e.g. CMS configuration files like `config/database.php`) cannot be read from other user accounts, even if they figure out absolute paths of your files. Setting permissions of such important files to 600 (read and write only to the owner and nothing to anyone else) is a good idea.

You can setup this protection in the file location `config/cms.php` in the section titled **Default permission mask**.

    /*
    |--------------------------------------------------------------------------
    | Default permission mask
    |--------------------------------------------------------------------------
    |
    | Specifies a default file and folder permission for newly created objects.
    |
    */

    'defaultMask' => ['file' => '644', 'folder' => '755'],

> **NOTE**: Don't forget to manually check to see if the files are already set to 644, as you may need to go into your cPanel and set them.

<a name="backend-only-mode"></a>
### Backend-only Mode

Winter CMS can be configured to run with only the backend module installed, therefore allowing Winter CMS to be used for self-contained applications. This may be useful for managing API data or administrating a headless CMS.

You can disable the CMS module from running by making the following changes to your `config/cms.php` file in your Winter CMS installation.

```php
'backendUri' => '', // Set to an empty string to use the root domain or subdomain.

'loadModules' => ['System', 'Backend'], // Remove 'Cms' to not use the CMS module.
```

This allows the root domain or subdomain that hosts your Winter CMS install to load up the backend immediately when accessing the URL in your browser.

After making these changes, you may delete the `modules/cms` folder from your project, as they will no longer be required.

If you have installed Winter CMS [via Composer](../help/using-composer), you can remove the `winter/wn-cms-module` line in the `require` block within the `composer.json` file in the root folder of your Winter CMS install, and this will prevent Composer from installing or updating the CMS module.

> **NOTE:** Some plugins may make references to classes within the CMS module. If this is the case, you will need to keep the CMS module files available in your install.

<a name="trusted-hosts"></a>
### Trusted Hosts

Winter CMS provides support for trusted hosts, a security feature that specifies the domains that your site will accept and respond to when receiving requests. Using this feature will prevent malicious users from phishing data from users or hijacking requests to your site.

By default, this feature is disabled as this sort of protection should generally be applied at the server configuration level, but can be enabled through the `trustedHosts` configuration value within `config/app.php`. By setting this value to `true`, your site will be set to only accept requests to the URL provided in `url` configuration value in the same file. You can, alternatively, specify an array of domains in which you will accept requests from if your site is available on multiple domains.

```php
'trustedHosts' => true,

'trustedHosts' => [
    'example.com',           // Matches just example.com
    'www.example.com',       // Matches just www.example.com
    '^(.+\.)?example\.com$', // Matches example.com and all subdomains
    'https://example.com',   // Matches just example.com
],
```

<a name="trusted-proxies"></a>
### Trusted Proxies

For sites that run through proxy hosts, such as CloudFlare or Amazon Elastic Load Balancing, Winter CMS can be configured to trust requests from proxies. This may be required in the case of secure HTTPS connections, as some proxies will terminate the SSL connection on their end before sending the (insecure) request to your host server.

If your site has SSL connections being forced (ie. you have enabled the `cms.backendForceSecure` configuration) without trusted proxies, the user's request may be declined or even forced into a redirect loop.

To enable trusted proxies, you can set the `trustedProxies` and `trustedProxyHeaders` configuration values in `config/app.php`.

The `trustedProxies` value specifies the IP address(es) that proxy connections will be accepted from, either as a comma-separated string or an array. You may also use `'*'` to allow all proxy requests.

```php
// To trust all proxies:
'trustedProxies' => '*',

// To trust two IP addresses as proxies
'trustedProxies' => '192.168.1.1, 192.168.1.2',
'trustedProxies' => ['192.168.1.1', '192.168.1.2'],
```

The `trustedProxyHeaders` value specifies which headers will be allowed to define the request when forwarded from the proxy. By default, you would generally allow all headers, but you may fine-tune this to your specific needs.

```php
// To trust all headers
'trustedProxyHeaders' => Illuminate\Http\Request::HEADER_X_FORWARDED_ALL,

// To trust only the hostname
'trustedProxyHeaders' => Illuminate\Http\Request::HEADER_X_FORWARDED_HOST,

// To trust the hostname, IP and port
'trustedProxyHeaders' => Illuminate\Http\Request::HEADER_X_FORWARDED_FOR
    | Illuminate\Http\Request::HEADER_X_FORWARDED_HOST
    | Illuminate\Http\Request::HEADER_X_FORWARDED_PORT
```

> **NOTE:** Amazon Elastic Load Balancing users must use the `HEADER_X_FORWARDED_AWS_ELB` option to accept the correct headers.
> ```php
> 'trustedProxyHeaders' => Illuminate\Http\Request::HEADER_X_FORWARDED_AWS_ELB
> ```

<a name="environment-config"></a>
## Environment configuration

<a name="base-environment"></a>
### Defining a base environment

It is often helpful to have different configuration values based on the environment the application is running in. You can do this by setting the `APP_ENV` environment variable which by default it is set to **production**. There are two common ways to change this value:

1. Set `APP_ENV` value directly with your webserver.

    For example, in Apache this line can be added to the `.htaccess` or `httpd.config` file:

        SetEnv APP_ENV "dev"

2. Create a **.env** file in the root directory with the following content:

        APP_ENV=dev

In both of the above examples, the environment is set to the new value `dev`. Configuration files can now be created in the path **config/dev** and will override the application's base configuration.

For example, to use a different MySQL database for the `dev` environment only, create a file called **config/dev/database.php** using this content:

    <?php

    return [
        'connections' => [
            'mysql' => [
                'host'     => 'localhost',
                'port'     => '',
                'database' => 'database',
                'username' => 'root',
                'password' => ''
            ]
        ]
    ];

<a name="domain-environment"></a>
### Domain driven environment

Winter supports using an environment detected by a specific hostname. You may place these hostnames in an environment configuration file, for example, **config/environment.php**.

Using this file contents below, when the application is accessed via **global.website.tld** the environment will be set to `global` and likewise for the others.

    <?php

    return [
        'hosts' => [
            'global.website.tld' => 'global',
            'local.website.tld' => 'local',
        ]
    ];

<a name="dotenv-configuration"></a>
### Converting to DotEnv configuration

As an alternative to the [base environment configuration](#base-environment) you may place common values in the environment instead of using configuration files. The config is then accessed using [DotEnv](https://github.com/vlucas/phpdotenv) syntax. Run the `winter:env` command to move common config values to the environment:

    php artisan winter:env

This will create an **.env** file in project root directory and modify configuration files to use `env` helper function. The first argument contains the key name found in the environment, the second argument contains an optional default value.

    'debug' => env('APP_DEBUG', true),

Your `.env` file should not be committed to your application's source control, since each developer or server using your application could require a different environment configuration.

It is also important that your `.env` file is not accessible to the public in production. To accomplish this, you should consider using a [public folder](#public-folder).
