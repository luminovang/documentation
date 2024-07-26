# Sitemap Generator

***

## Overview

Generate an XML sitemap for your website using Luminova's sitemap generate command in CLI.

***

## Introduction

Generating a sitemap for your website with Luminova Framework is straightforward. Follow these simple steps.

Open Command Line Interface (CLI): Navigate to your project directory in the command line interface and execute the following command in the CLI to generate the sitemap:

```bash
php novakit generate:sitemap
```

**Configure Sitemap Settings (Required)**

Customize your sitemap settings according to your project requirements. You will need to configure your project URL, optionally define your URL ignores or set the maximum scanning depth.

**Configuration**

Open the sitemap configuration file located at `/app/Config/Sitemap.php`, and modify the settings as needed.

> After configuring your settings, rerun sitemap generation command (`php novakit generate:sitemap`) to apply the changes

***

* Class namespace: `\Luminova\Seo\Sitemap`

## Methods

An alternative method to generate sitemap within your code.

### generate

Generate sitemap XLM file for your application.

```php
public static generate(null|string $url = null, null|\Luminova\Base\BaseConsole $cli = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **null&#124;string** | Application start URL  |
| `$cli` | **null&#124;\Luminova\Base\BaseConsole** | Luminova Console instance |