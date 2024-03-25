# Installation

- [docker-compose.yml](#docker-compose)
- [Dockerfile](#dockerfile)
- [nginx](#nginx)
- [.env](#.env)
- [.vscode](#.vscode)
- [Combine all together](#file-hierarchy)

<div class="og-description">
    Docker development environment Documentation.
</div>

How to setup a Docker development environment for WinterCMS. If you want to understand all the details below please read those articles:
 - https://www.digitalocean.com/community/tutorials/how-to-install-and-set-up-laravel-with-docker-compose-on-ubuntu-22-04
 - https://blog.devsense.com/2019/debugging-php-on-docker-with-visual-studio-code

<a name="docker-compose"></a>
## docker-compose.yml

It is the main file that links all pieces together and configures them.

```yaml
version: '3'
services:
    winter:
      build:
        args:
          user: wintercms
          uid: 1000
        context: ./
        dockerfile: docker/Dockerfile
      image: wintercms
      container_name: php-${APP_NAME}
      restart: unless-stopped
      working_dir: /var/www/html
      volumes:
        - ./html/:/var/www/html
        - ./logs:/var/log
      environment:
        XDEBUG_MODE: debug
        XDEBUG_CONFIG: client_host=host.docker.internal client_port=9003 log=/var/log/xdebug.log start_with_request=yes
      extra_hosts:
        - "host.docker.internal:host-gateway"
      networks:
        - internal
    nginx:
        image: nginx:alpine
        container_name: nginx-${APP_NAME}
        restart: unless-stopped
        ports:
            - "8000:80"
        volumes:
            - ./html:/var/www/html
            - ./docker/nginx:/etc/nginx/conf.d
        networks:
          - internal
        depends_on:
            - winter
            - db
    db:
        image: mysql:5.7
        container_name: db-${APP_NAME}
        restart: unless-stopped
        environment:
          MYSQL_DATABASE: ${DB_DATABASE}
          MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
          MYSQL_USER: ${DB_USERNAME}
          MYSQL_PASSWORD: ${DB_PASSWORD}
          SERVICE_TAGS: dev
          SERVICE_NAME: mysql
        ports:
            - "3306:3306"
        networks:
          - internal

networks:
  internal:
    driver: bridge
```

<a name="dockerfile"></a>
## Dockerfile

```Dockerfile
FROM php:8.1-fpm

# Arguments defined in docker-compose.yml
ARG user
ARG uid

# Install system dependencies
RUN apt-get update && apt-get install -y \
    cron \
    git-core \
    jq \
    unzip \
    zip \
	libjpeg-dev \
    libpng-dev \
    libpq-dev \
    libsqlite3-dev \
    libwebp-dev \
    libzip-dev

# Install xedbug
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-configure zip --with-zip
RUN docker-php-ext-configure gd --with-jpeg --with-webp
RUN docker-php-ext-install exif gd mysqli opcache pdo_pgsql pdo_mysql zip

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Create system user to run Composer and Artisan Commands
RUN useradd -G www-data,root -u $uid -d /home/$user $user
RUN mkdir -p /home/$user/.composer && \
    chown -R $user:$user /home/$user

# Set working directory
WORKDIR /var/www/html
# Set user
USER $user
```

<a name="nginx"></a>
## Nginx

```conf

server {
    listen 80;
    index index.php index.html;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /var/www/html;
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass winter:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root/$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        gzip_static on;
    }
}
```

<a name=".env"></a>
## .env

```
APP_NAME=WinterCMS
APP_ENV=dev
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost:8000

MYSQL_VERSION=5.7
DB_PORT=3306
DB_DATABASE=winter
DB_USERNAME=winter
DB_PASSWORD=password
```

<a name=".vscode"></a>
## .vscode launch.json

```json
{
    "version": "0.2.0",
    "configurations": [
        
        {
            "name": "Launch built-in server and debug",
            "type": "php",
            "request": "launch",
            "runtimeArgs": [
                "-S",
                "localhost:8000",
                "-t",
                "."
            ],
            "port": 9003,
            "serverReadyAction": {
                "action": "openExternally"
            }
        },
        {
            "name": "Debug current script in console",
            "type": "php",
            "request": "launch",
            "program": "${file}",
            "cwd": "${fileDirname}",
            "externalConsole": false,
            "port": 9003,
            "pathMappings": {
                "/var/www/html": "${workspaceFolder}/html"
            },
        },
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "externalConsole": false,            
            "port": [9000,9003],
            "pathMappings": {
                "/var/www/html": "${workspaceFolder}/html"
            }
        }
    ]
}
```

<a name="file-hierarchy"></a>
## Combine all together

The expected file structure of the above files and configs.

```
.
.
├── .env
├── .vscode
│   └── launch.json
├── docker
│   ├── Dockerfile
│   └── nginx
│       └── default.conf
├── docker-compose.yml
└── html    # you WinterCMS files
    ├── artisan
    ├── themes
    ├── ...
    └── vendor
```
