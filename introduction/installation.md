# Installation and Setup Guides

***

## Overview

Installation guides for the PHP Luminova framework highlight the benefits of using Composer for easy maintenance and ensuring you're always up to date with the latest framework and dependency.

***

## Introduction

Whether you're new to the framework or a seasoned developer, getting started with Luminova is a breeze.  In this guide, we'll walk you through the installation process.

For easy maintenance and keeping Luminova up to date, it's recommended to install it using Composer, although manual download is also possible. Composer ensures that you have the latest version of the framework and its dependencies.

***

### Composer Installation

Installing via [Composer](https://getcomposer.org/doc/00-intro.md) is recommended to keeps Framework up to date easily.

```bash
composer create-project luminovang/luminova my-project
```
To install in the current working directory.

```bash
composer create-project luminovang/luminova
```

***

###  Git Installation

Installing via git command.

```bash
$ git clone https://github.com/luminovang/luminova
$ composer install
$ composer test
```

***

### Manual Download

To manually download and install the Luminova framework, follow these steps:

**Download the Latest or Current Version:**

1. Click on the [Download Luminova Framework](/download/) link to get the latest version or the version currently displayed.
2. Extract the ZIP file to your project directory.
3. Start your Luminova PHP development server by running the novakit command, or use a local server like `XAMPP`, `WAMP`, or similar.
4. Open the project folder in your browser (e.g., `http://localhost/project/public` or `http://192.168.0.1:8080`) to start using Luminova.

**Download Other Versions:**

To access previous or switch versions of the Luminova framework:

1. Select the desired version from the version selector at the top right of the page.
2. Return to this installation page and click the download link to get the chosen version.

***

**GitHub**

If you choose to download manually from GitHub, here are the steps to follow.

1. First you will need to [Download Framework Scaffolding](https://github.com/luminovang/luminova/releases) and extract the zip to your project directory.
2. Then you will need to [Download Framework Codes](https://github.com/luminovang/framework/releases) and extract in a separate directory, then navigate to `src` directory, copy all the files and folders except for `plugins` directory, to the framework scaffolding `system` directory.
3. Now set your `hostname` and `application name` in `env` file.
4. Open your project localhost url (e.g. http://localhost/project-path), in a browser.

***

### Server Configuration

Let's start by creating the first website using the Luminova framework.

The first thing to do is configure your web server to use a custom document root which should be the path to framework controller and other browser accessible files. e.g:  `/path/to/your/project/public`.

Assuming your existing document root is `/var/www/public_html` or `/var/www/html`, now you have to change it to `/var/www/project/public` or `/var/www/example.com/public`. The `project` or `example.com` directory will serve as your private document root where the framework files will be located which is not accessible from web browsers, while the `public` will be accessible in browser.

***

### VirtualHost Samples

An example configuration of a virtual host file may look like below depending on your server configuration.

#### Apache

Apache VirtualHost configuration example.

```xml
<VirtualHost *:80>
    ServerAdmin admin@example.com
    ServerName example.com
    ServerAlias www.example.com
    DocumentRoot "/var/www/example.com/public"

    # Logs inside writable/log
    ErrorLog     "/var/www/example.com/writeable/log/server/error_log"
    CustomLog    "/var/www/example.com/writeable/log/server/access_log" combined

    <Directory "/var/www/example.com/public">
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    # Optional: Set custom php.ini path (if mod_php is used)
    <IfModule mod_php.c>
        SetEnv PHPRC /var/www/example.com/php.ini
    </IfModule>
</VirtualHost>
```
***

#### Nginx

Nginx VirtualHost configuration example.

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/example.com/public;
    index index.php;

    access_log /var/www/example.com/access_log;
    error_log  /var/www/example.com/error_log;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;

        # Optional: security headers for PHP
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # Block access to .ht* and hidden files
    location ~ /\.ht {
        deny all;
    }
}
```

***

### Project Structure

![Project Structure](/assets/images/structure.png.md)

The above configuration assumes the project folder is located as follows:

```bash
/var/www/
    └── example.com                         (Framework Document Root)
        ├── app/                            (Application configurations, utilities, models, and other files)
        │   ├── Config/                     (Application configuration classes)
        │   ├── Console/                    (Custom Novakit CLI console classes)
        │   ├── Database/                   (Database migration and seeder classes)
        │   ├── Models/                     (MVC or Default models classes)
        │   ├── Languages/                  (Language localization files)
        │   ├── Utils/                      (Custom classes and developer global fynctions)
        │   ├── Errors/                     (Application error constrollers and files)
        │   │   ├── Controllers/            (Error controller classes)
        │   │   └── Defaults/               (Default error pages)
        │   ├── Controllers/                (MVC controllers for handling application logic)
        │   │   ├── Http/                   (Controllers for handling HTTP requests)
        │   │   └── Cli/                    (Controllers for handling CLI commands)
        │   └── Modules/                    (HMVC modules)
        │       ├── Controllers/            (Global controllers for HMVC modules)
        │       │   ├── Http/               (Controllers for handling HTTP requests)
        │       │   └── Cli/                (Controllers for handling CLI commands)
        │       ├── Models/                 (Models for HMVC modules)
        │       ├── Views/                  (Views for HMVC modules)
        │       └── <Module>/               (Custom HMVC module with a lowercase name)
        │           ├── Controllers/        (Controllers specific to the module)
        │           │   ├── Http/           (Controllers for handling HTTP requests)
        │           │   └── Cli/            (Controllers for handling CLI commands)
        │           ├── Models/             (Module-specific models for handling data)
        │           └── Views/              (Module-specific views)
        ├── bin/                            (Executable binary files and novakit console configuration)
        ├── public/                         (Document root for serving static content)
        │   ├── index.php                   (Front controller for handling incoming requests)
        │   ├── .htaccess                   (Htaccess configuration file)
        │   └── assets/                     (Public static assets such as CSS, JS, images)
        ├── libraries/                      (Custom libraries)
        │   └── libs/                       (Custom library files)
        ├── system/                         (Luminova framework core classes)
        │   └── plugins/                    (Third-party libraries or plugins)
        ├── resources/                      (Resource view templates)
        │   └── Views/                      (View templates files for rendering)
        └── writeable/                      (Writable directory for runtime data)
            ├── log/                        (Error log files)
            ├── storages/                   (Private storage directory)
            └── caches/                     (Cache directory)
```

> **IMPORTANT**
> Make sure you set the required permissions for `public` directory to be accessible in browsers.

---

### Htaccess Configuration

This file is intended to be placed in your `public/` directory. It enables URL rewriting and security best practices to ensure all requests are routed through the front controller (`index.php`), with additional enhancements for cleaner URLs and HTTPS enforcement.

**Configuration Description:**

Disable automatic directory listings for security.

```ini
# /public/.htaccess

<IfModule mod_autoindex.c>
    Options -Indexes
</IfModule>
```

---

Enable URL rewriting and set the default file to serve when accessing a directory (e.g., / => `index.php`).

```ini
# /public/.htaccess

RewriteEngine On
DirectoryIndex index.php
```

---

Redirect all `www` requests to non-www for canonical URLs (e.g, `www.example.com` to `example.com`).

```ini
# /public/.htaccess

RewriteCond %{HTTP_HOST} ^www\.(.+)$ [NC]
RewriteRule ^ https://%1%{REQUEST_URI} [L,R=301]
```

---

Preserve Authorization headers (useful for APIs that rely on `Authorization`).

```ini
# /public/.htaccess

RewriteCond %{HTTP:Authorization} .
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
```

---

Remove trailing slashes and skips `assets` and `well-known` paths from URLs for consistency.
This rule only applies to URLs that are not actual directories in `/public/` directory.

```ini
# /public/.htaccess

RewriteCond %{REQUEST_URI} !^/(assets|\.well-known)/
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)/$ /$1 [L,R=301]
```

---

Route all other requests to the front controller and skips static directories like `/assets`, `/.well-known`
or any valid file/directory that does exist in `/public/` directory.

```ini
# /public/.htaccess

RewriteCond %{REQUEST_URI} !^/(assets|\.well-known)/
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [L]
```

> **Note**
> This block is responsible for handling "pretty URLs" by forwarding any request
> that is not a real file or directory to `index.php` (your front controller).
>
> **Explanation of each condition:**
>
>> **%{REQUEST_URI} !^/(assets|\.well-known)/ :**
>>
>>    Skip routing if the request targets static paths like `/assets` or `/.well-known`.
>>    These are served directly and shouldn't be routed through Framework.
>>
>> **Exclusion:**
>>
>> Without the `REQUEST_URI` exclusion for `/assets` and `/.well-known`, 
>> a missing file in those directories would trigger your **application's** 404 page.
>> With it, Server will handle those cases with its default 404 instead, which is often preferable for static content.
>
>> **%{REQUEST_FILENAME} !-d :**
>>
>>    Skip routing if the requested path is an actual directory.
>
>> **%{REQUEST_FILENAME} !-f :**
>>
>>    Skip routing if the requested path is an existing file.

---

### Full Htaccess Example

```ini
# /public/.htaccess

# Disable directory listing for security
<IfModule mod_autoindex.c>
    Options -Indexes
</IfModule>

<IfModule mod_rewrite.c>
    RewriteEngine On

    # Set the default file to serve in a directory
    DirectoryIndex index.php

    # Redirect www to non-www (canonical URL)
    RewriteCond %{HTTP_HOST} ^www\.(.+)$ [NC]
    RewriteRule ^ https://%1%{REQUEST_URI} [L,R=301]

    # Enforce HTTPS
    RewriteCond %{SERVER_PORT} !^443$
    RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

    # Preserve Authorization headers (e.g., for APIs)
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    # Remove trailing slash (except for actual directories or excluded paths)
    RewriteCond %{REQUEST_URI} !^/(assets|\.well-known)/
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)/$ /$1 [L,R=301]

    # Route all requests to the front controller
    RewriteCond %{REQUEST_URI} !^/(assets|\.well-known)/
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

</IfModule>

# Hide Apache server signature for security
ServerSignature Off
```

***

### Build Project

To package your application for publishing, you can use the Novakit command to archive the project files required for production. Run the following command in a CLI tool:

```bash 
php novakit build:project --type zip
```

After configuring the document root and archiving your project files, proceed to upload the latest version of your project, located in the `/builds/v-{*.*}` folder, to your production server. You can upload it to either the `/user/var/www/example.com` directory, depending on your setup.