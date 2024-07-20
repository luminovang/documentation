# Constant Variables

***

## Overview

Global constant variables, utilizing global constant variables, to improve code clarity and maintainability.

***

## Introduction

Global constant variables play a pivotal role in software development by providing a mechanism for defining values that remain consistent throughout the entire application. Unlike regular variables, which can be modified during runtime, constant variables maintain their assigned values throughout the execution of the program, ensuring consistency and predictability.

Constants serve as descriptive placeholders for values that are used repeatedly within the application, such as configuration settings, error codes, or predefined values. By assigning meaningful names to constants, developers can convey the purpose and significance of these values, making the code more readable and understandable.

#### Scope

Global constant variables are accessible from any part of the application, making them suitable for storing values that are used across multiple modules.

### Status Codes

- `STATUS_OK` *(int)*: - Success status code.
- `STATUS_ERROR` *(int)*: - Error status code.

***

### Application

- `APP_NAME` *(string)*: - Application development state.
- `APP_VERSION` *(string)*: - Home directory path.
- `APP_FILE_VERSION` *(string)*: - Home directory path.
- `APP_FILE_VERSION` *(string)*: - Home directory path.

***

### Environment

- `ENVIRONMENT` *(string)*: - Application environment state type.
- `PRODUCTION` *(bool)*: - Application production state.
- `MAINTENANCE` *(bool)*: - Application on maintenance mode.
- `CLI_ENVIRONMENT` *(string)*: - Application CLI development state.
- `NOVAKIT_ENV` *(string|null)*: - NovaKit CLI executable script.

***

### I/O Streams

- `STDOUT` *(string)*: - Standard output stream.
- `STDIN` *(string)*: - Standard input stream.
- `STDERR` *(string)*: - Standard error stream.

***

### File Paths

- `FRONT_CONTROLLER` *(string)*: - Front controller path.
- `APP_ROOT` *(string)*: - Application document root path.
- `DOCUMENT_ROOT` *(string)*: - Document root path.

> The application root (`APP_ROOT`) and document root (`DOCUMENT_ROOT`) are similar, but their values depend on the context.
> 
> In a development environment, `APP_ROOT` typically points to something like `/Applications/XAMPP/xamppfiles/htdocs/my-project/`, whereas `DOCUMENT_ROOT` usually points to `/`.
> 
> > To ensure consistency across development and production environments by utilizing `APP_ROOT` to represent the application root directory.

***

### URLs

- `APP_HOSTNAME` *(string)*: - Application hostname.
- `APP_URL` *(string)*: - Application request url (e.g.: http://example.com).
- `APP_WWW_HOSTNAME` *(string)*: - Application hostname with `www`.
- `APP_WWW_URL` *(string)*: - Application request `www` url (e.g.: http://www.example.com).
- `URL_SCHEME` *(string)*: - Url protocol scheme string `http` or `https`.

***

### Database Fetch Modes

- `FETCH_ASSOC` *(int)*: - Fetch as an associative array.
- `FETCH_NUM` *(int)*: - Fetch as an array integer index.
- `FETCH_BOTH` *(int)*: - Fetch as an array integer index and associative.
- `FETCH_OBJ` *(int)*: - Fetch as an object.
- `FETCH_COLUMN` *(int)*: - Fetch as an array columns integer index.
- `FETCH_NUM_OBJ` *(int)*: - Fetch as an object with string integer property names.
- `FETCH_ALL` *(int)*: - Fetch all as an associative array.
- `FETCH_COLUMN_ASSOC` *(int)*: - Fetch all as an associative array.

***

### Database Results Modes

- `RETURN_NEXT` *(int)*: - Fetch next or single record.
- `RETURN_2D_NUM` *(int)*: - Fetch all as 2D array integers.
- `RETURN_ID` *(int)*: - Fetch last inserted ID.
- `RETURN_INT` *(int)*: - Fetch count of records.
- `RETURN_COUNT` *(int)*: - Fetch number of affected rows.
- `RETURN_COLUMN` *(int)*: - Fetch all result columns.
- `RETURN_ALL` *(int)*: - Fetch all results.
- `RETURN_STMT` *(int)*: - Return prepared statement.