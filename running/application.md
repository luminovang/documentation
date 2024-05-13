##  Development Server

***

## Overview

Luminova&#039;s local development server, leveraging PHP&#039;s built-in web server.

***

Luminova provides a convenient built-in development server, leveraging PHP's native web server functionality. You have the option to use this server for local development or continue using platforms like XAMPP or WAMPP.

***

### PHP Dev Server

To launch the Luminova PHP development server, navigate to the main directory of your project and run the following command:

```bash
php novakit server
```

> This will start the server, and you can access your application in your browser at `http://localhost:8080`.

***

### Command Options

You can customize the server configuration by specifying a hostname and port:

```bash
php novakit server --host=127.0.0.1 --port=8081
```

> Passing your hostname and port will start the server and you can now view your application in your browser at `http://127.0.0.1:8081`.

Additionally, you can specify a specific PHP version to use with the `--php` option, the value should specify the path to the PHP executable binary version you want to use:

```bash
php novakit server --php=/path/to/php.binary
```

***

### Third Party Dev Server

If you prefer to use third-party development servers like XAMPP or WAMPP, simply install and start the respective application services. Then, navigate to the `htdocs` directory in `XAMPP` or the `www` directory in `WAMPP` and create your project directory (e.g., myproject.com).

You can access your project by navigating to `http://localhost/myproject.com/public/` or `http://127.0.0.1/myproject.com/public/`, in your web browser, which will launch your project.