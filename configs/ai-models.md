# Artificial Intelligence Models Configuration

***

## Overview

Set up and manage AI models used in your application — choose models, define behavior, and customize how your app interacts with AI services.

***

## Introduction

These settings define the core options you need to configure before using the `Luminova\Utility\Ai\Models\OpenAI` API to work with AI models. Make sure this class is properly set up to avoid authentication or request issues.

***

## Class Definition

* Class namespace: `\App\Config\AI`
* File path: `/app/Config/AI.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

### handler

The `AI` handler class (default: `OpenAI`).

```php
public string $handler = 'OpenAI';
```

> Currently only `OpenAI` is supported.

***

### apiKey

API key for authenticating OpenAI requests.

To register an account on the `OpenAI` platform and receive an API key, visit [https://platform.openai.com/](https://platform.openai.com/).

```php
public string $apiKey = '';
```

***

### organization

OpenAI organization ID (if your account belongs to one) (e.g, `org-xxxx`).

```php
public ?string $organization = null;
```

**Reference**

- [API Reference & Authentication](https://platform.openai.com/docs/api-reference/authentication)
- [Organization & General](https://platform.openai.com/settings/organization/general)

***

### project

Optional, OpenAI project ID to group API usage.

```php
public ?string $project = null;
```

***

### version

OpenAI API version to use (default: `v1`).

```php
public string $version = 'v1';
```