# Development Server

***

## Overview

Luminova PHP development server with the novakit command is useful for local development and testing your application in a controlled environment.

***

## Introduction

Luminova provides a convenient built-in development server, leveraging PHP's native web development server functionality. This is useful for local development and testing your application in a controlled environment. You have the option to use this server for local development or continue using platforms like `XAMPP` or `WAMPP`.

***

### Help
To display help related information for development server run the below command.

```bash
php novakit server --help
```

### Server Command

The `server` command starts the Luminova PHP development server. To start server, navigate to your project main directory and run the following command:

```bash
php novakit server
```

> This will start the server, allowing you to access your application over the browser by visiting `http://localhost:8080`.

***

### Command Options

You can customize the server startup with the following options:

- `-b, --php`: Specify the PHP binary location to use.
- `-h, --host`: Specify the HTTP hostname to use.
- `-p, --port`: Specify the HTTP host port to use.

***

Specifying a hostname and port:

```bash
php novakit server --host=127.0.0.1 --port=8081
```

> Passing your hostname and port will start the server and you can now view your application in your browser at `http://127.0.0.1:8081`.

Specifying the `PHP` version to use.

```bash
php novakit server --php=/path/to/php.binary
```
> The value should be the path to the PHP executable binary version you want to use:

***

### Third Party Server

If you prefer to use third-party development servers like `XAMPP` or `WAMPP`, simply install and start the respective application services. Then, navigate to the `htdocs` directory in `XAMPP` or the `www` directory in `WAMPP` and create your project directory (e.g., myproject.com).

You can access your project by navigating to `http://localhost/myproject.com/public/` or `http://127.0.0.1/myproject.com/public/`, in your web browser, which will launch your project.