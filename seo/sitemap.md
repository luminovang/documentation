# Programmatically Integrate the Sitemap Generator

***

## Overview

Generate an XML sitemap for your website using Luminova's sitemap generate command in CLI.

***

## Introduction

Generating a sitemap for your website in Luminova Framework is straightforward. Follow these simple steps.

Open Command Line Interface (CLI): Navigate to your project directory in the command line interface and execute the following command in the CLI to generate the sitemap:

```bash
php novakit generate:sitemap
```

---

### Configuration

Open the sitemap configuration class located at `/app/Config/Sitemap.php`, and modify the properties as needed.

According to your project requirements. You will need to configure your project start `URL`, optionally define ignore URLs, maximum scan depth and execution time.

> After configuring your settings, rerun sitemap generation command (`php novakit generate:sitemap`) to apply the changes.

***

### Programmatic Implementation

This example shows how to programmatically implement a custom sitemap generator in application and schedule it to run periodically using a cron job or similar in production environments.

#### 1 Command Controller

The `SitemapCommand` class demonstrates how to generate a sitemap using Luminova's `Sitemap` generator class.

```php
// /app/Controllers/Cli/SitemapCommand.php

namespace App\Controllers\Cli;

use Luminova\Base\BaseCommand;
use Luminova\Seo\Sitemap;
use Exception;

class SitemapCommand extends BaseCommand
{
    /**
     * Generates the sitemap for the website.
     *
     * @return int Status code indicating success or failure.
     */
    public function generator(): int 
    {
        try {
            // Generate sitemap for the provided base URL
            if (Sitemap::generate(APP_URL, $this->term, 'sitemap.xml')) {
                echo 'Sitemap was successfully generated.';
                return STATS_SUCCESS; 
            }

            // If generation fails, display an error message
            echo 'Failed to generate sitemap.';
        } catch (Exception $e) {
            // Handle any unexpected exceptions
            echo 'Error generating sitemap: ' . $e->getMessage();
        }

        return STATS_ERROR;
    }
}
```

---

#### 2 Cron Task Schedulling

The `Cron` configuration class schedules the `SitemapCommand::generator` method to run periodically.

```php
// /app/Config/Cron.php

namespace App\Config;

use Luminova\Core\CoreCronTasks;

final class Cron extends CoreCronTasks
{
    /**
     * Schedules the task for periodic sitemap generation.
     */
    protected function schedule(): void 
    {
        // Schedule other service tasks...
        
        // Add a new task for generating the sitemap
        $this->service('\App\Controllers\Cli\SitemapCommand::generator')
            ->seconds(5) // Runs the task every 5 seconds (example; adjust as needed)
            ->output(root('/writeable/logs/') . 'cron.log') // Log output path
            ->log('debug'); // Debug-level logging
    }
}
```

---

#### 3 Cron Job Execution

To run the sitemap generation task every 24 hours, configure a cron job on the server.

```bash
# Run the scheduled tasks in Luminova every 24 hours
0 0 * * * /usr/local/cpanel/3rdparty/bin/php /home/www/example.com/novakit cron:run >> /dev/null 2>&1
```

- **Explanation**:
  - `0 0 * * *`: Schedule to run at midnight every day.
  - `/usr/local/cpanel/3rdparty/bin/php`: Path to PHP executable.
  - `/home/www/example.com/novakit cron:run`: Executes the Luminova cron task runner.
  - `>> /dev/null 2>&1`: Suppresses output and redirects errors to `/dev/null`.

---

###  Summary  

1. **Sitemap Generation**: The `SitemapCommand` class handles sitemap creation for a specific website URL.
2. **Task Scheduling**: The `Cron` configuration ensures the sitemap generation is run automatically at defined intervals.
3. **Server Cron Job**: A server-side cron job executes Luminova's scheduled tasks at regular intervals.

***

### Class Definition

* Class namespace: `\Luminova\Seo\Sitemap`

***

## Methods

An alternative method to generate sitemap within your code.

### generate

Generate am XML sitemap and store in in `public` directory.

```php
public static generate(
    ?string $url = null, 
    Luminova\Command\Terminal|Luminova\Interface\LazyInterface<\Luminova\Command\Terminal>|null $term = null, 
    string $basename = 'sitemap.xml'
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **null&#124;string** | The start URL to generate sitemap of (default: null).  |
| `$term` | **Terminal&#124;LazyInterface<Terminal>&#124;null** | The terminal instance, to use when generating site map in cli (default: null) |
| `$basename` | **string** | The base name to save generated sitemap as (e.g, `sitemap.xml`). |

**Return:**

- `bool` - Return true if successful, false otherwise.

**Throws:**

- `Luminova\Exceptions\RuntimeException` - If tries to call in none cli environment.