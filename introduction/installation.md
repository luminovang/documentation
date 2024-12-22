# Installation Guides

***

## Overview

Installation guides for the PHP Luminova framework highlight the benefits of using Composer for easy maintenance and ensuring you're always up to date with the latest framework and dependency.

***

## Introduction

Whether you're new to the framework or a seasoned developer, getting started with Luminova is a breeze.  In this guide, we'll walk you through the installation process

For easy maintenance and keeping Luminova up to date, it's recommended to install it using Composer, although manual download is also possible. Composer ensures that you have the latest version of the framework and its dependencies.

***

### Composer Installation

Installing via [Composer](https://getcomposer.org/doc/00-intro.md) is recommended to keeps Framework up to date easily.

```bash
composer create-project luminovang/luminova my-project
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

Assuming your existing document root is `/var/www/public_html` now you have to change it to `/var/www/project/public` or `/var/www/public_html/public`. The `project` or `public_html` directory will serve as your private document root where the framework files will be located which is not accessible from web browsers, while the `public` will be accessible in browser.

***

### VirtualHost Samples

An example configuration of a virtual host file may look like below depending on your server configuration.

#### Apache

Apache VirtualHost configuration example.

```bash 
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot "/var/www/public_html/public"
    ErrorLog     "/var/www/public_html/error_log"
    CustomLog    "/var/www/public_html/access_log" common
    <Directory "/var/www/public_html/public">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```
***

#### Nginx

Nginx VirtualHost configuration example.

```bash
server {
    listen 80;
    server_name example.com;
    root /var/www/example.com/public_html/public;
    access_log /var/www/example.com/public_html/access_log;
    error_log /var/www/example.com/error_log;
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }
    location ~ /\.ht {
        deny all;
    }
}
```

***

### Project Structure

The above configuration assumes the project folder is located as follows:

```bash
/var/www/example.com/
    └── public_html/                        (Framework Document Root)
        ├── app/                            (Application configurations, utilities, models, and other files)
        │    ├── Config/                    (Application configuration classes)
        │    ├── Controllers/               (MVC controllers for handling application logic)
        │    │    ├── Http/                 (Controllers for handling HTTP requests)
        │    │    └── Cli/                  (Controllers for handling CLI commands)
        │    └── Modules/                   (HMVC modules)
        │         ├── Controllers/          (Global controllers for HMVC modules)
        │         │    ├── Http/            (Controllers for handling HTTP requests)
        │         │    └── Cli/             (Controllers for handling CLI commands)
        │         ├── Models/               (Models for HMVC modules)
        │         ├── Views/                (Views for HMVC modules)
        │         └── <module>/             (Custom HMVC module with a lowercase name)
        │              ├── Controllers/     (Controllers specific to the module)
        │              │    ├── Http/       (Controllers for handling HTTP requests)
        │              │    └── Cli/        (Controllers for handling CLI commands)
        │              ├── Models/          (Module-specific models for handling data)
        │              └── Views/           (Module-specific views)
        ├── bin/                            (Executable binary files)
        ├── public/                         (Document root for serving static content)
        │    ├── index.php                  (Front controller for handling incoming requests)
        │    └── assets/                    (Public static assets such as CSS, JS, images)
        ├── libraries/                      (Custom libraries)
        │    └── libs/                      (Custom library files)
        ├── system/                         (Luminova framework core classes)
        │    └── plugins/                   (Third-party libraries or plugins)
        ├── resources/                      (Resource view templates)
        │    └── Views/                     (View templates files for rendering)
        └── writeable/                      (Writable directory for runtime data)
             ├── log/                       (Error log files)
             ├── storages/                  (Private storage directory)
             └── caches/                    (Cache directory)
```

> **IMPORTANT**
> Make sure you set the required permissions for `public` directory to be accessible in browsers.

***

### Build Project

To package your application for publishing, you can use the Novakit command to archive the project files required for production. Run the following command in a CLI tool:

```bash 
php novakit build:project --type zip
```

After configuring the document root and archiving your project files, proceed to upload the latest version of your project, located in the `/builds/v-{*.*}` folder, to your production server. You can upload it to either the `/user/var/www/public_html/` or `/user/var/www/` directory, depending on your setup."