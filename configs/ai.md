# AI's Configuration

***

## Overview

The FileDelivery class simplifies secure file delivery from a private storage location to the user's web browsers including generating temporal URLs for file previews.

***

## Introduction

These properties serve as essential configurations needed to set up before using `OpenAI` APIs to interact with `AI` models.

***

* Class namespace: `\App\Config\AI`
* File path: `/app/Config/AI.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

### handler

The `AI` handler class (default: `OpenAI`).
Currently only OpenAI is supported.

```php
public string $handler = 'OpenAI';
```

***

### apiKey

Your `AI` provider's API key (Required).
To register an account on the `OpenAI` platform and receive an API key, visit [https://platform.openai.com/](https://platform.openai.com/).

```php
public string $apiKey = '';
```

***

### organization

Optionally set your OpenAI Organization ID.

```php
public ?string $organization = null;
```

**Reference**

- [API Reference & Authentication](https://platform.openai.com/docs/api-reference/authentication)
- [Organization & General](https://platform.openai.com/settings/organization/general)

***

### project

Optionally set your OpenAI Project ID.

```php
public ?string $project = null;
```

***

### version

Optionally set your OpenAI API version (default: `v1`).

```php
public string $version = 'v1';
```