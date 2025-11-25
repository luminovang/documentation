# Website Sitemap &amp; Broken Link Generator

***

## Overview

Generate XML sitemaps or scan for broken links using Luminova’s Sitemap Generator, either via the novakit CLI or directly in your code.

***

## Introduction

Luminova's Sitemap Generator is a built-in system that not only creates **XML sitemaps** for your application but can also **scan and report broken links** across your site. It helps you maintain both **search engine visibility** and **link integrity** in a single process.

You can generate a sitemap or a broken link report in three ways:

1. **Command Line (recommended):**
   Use the `novakit` CLI tool to quickly generate or update a sitemap, or to perform a broken link scan.

2. **Programmatically:**
   Call the sitemap generator directly from your code using the `Sitemap::generate()` method.

3. **Background Queue:**
   Run sitemap generation as a background process in production environments to avoid blocking requests.

---

### Supported Novakit CLI Options

```bash
-u,  --url              # Specify the starting URL to scan (default: value of `env(dev.app.start.url)`).
-f,  --basename         # Set the output filename (default: `sitemap.xml` or `broken.sitemap.json`).
-b,  --broken           # Scan and generate a JSON report of broken links instead of a sitemap. 
-t,  --link-tree        # Generate a TXT report of website links instead of an XML sitemap or JSON broken links.
--format                # Custom output format for link tree, e.g., "{url} | {title} | {status} | {lastmod}". Default: "{url} {title} ({status})"
-l,  --limit            # Limit the number of URLs to scan (0 = no limit).   
-d,  --delay            # Delay in seconds between scans (minimum: 1).
-e,  --max-execution    # Maximum script execution time in seconds (0 = unlimited).
-p,  --prefix           # Restrict scan to URLs matching a specific prefix.
-c,  --change           # Set the expected change frequency (always, daily, weekly, etc.).
-s,  --html             # Include static `.html` versions of URLs in the sitemap. 
-v,  --verbose          # Control output verbosity (use `-v` multiple times for more detail). 
-n,  --dry-run          # Perform a full scan without writing any output files.
-a,  --ignore-assets    # Ignore URLs pointing to assets directories such as `/assets/` or `/public/assets/`.
-h,  --help             # Show help information for this command.
```

**Get Help:**

To see all available options and learn more about the sitemap generator:

```bash
php novakit sitemap --help
```

---

### 1. Configuration

Generating a sitemap or checking for broken links in your web application is simple. 
[Open the sitemap configuration file](/configs/sitemap.md):

```
/app/Config/Sitemap.php
```

Update the properties to match your project needs. Common settings include:

* **Start URL** - The base URL to begin scanning.
* **Ignore URLs** - URLs you don't want included in the sitemap.
* **Maximum scan depth** - Limit how many URLs to scan.
* **Execution time** - Maximum time allowed for sitemap generation.

---

### 2. Generating Sitemap

Navigate to your project directory in the command line interface (CLI) and run the following command to generate your sitemap:

```bash
php novakit sitemap
```

---

### Cron Execution in Production

To automatically generate sitemaps every **2 days**, configure a cron job like this:

```bash
# Run sitemap generation every 2 days at 5:00 AM
0 5 */2 * * flock -n /tmp/task.lock /usr/bin/php /path/to/project/novakit sitemap
```

**Explanation:**

* `0 5 */2 * *` → Executes at 05:00 every 2 days.
* `flock` → Ensures only one instance runs at a time to prevent overlapping processes.
* `/usr/bin/php /path/to/project/novakit sitemap` → Calls the Luminova sitemap generator.

> This is the recommended production approach for scheduling sitemaps automatically.

***

## Programmatic Implementation

You can programmatically generate sitemaps in Luminova and schedule them to run periodically using a background task queue or your own console command. Both options are production-ready.

---

### Task Queue (Recommended)

This is the simplest way to run the sitemap generator automatically at fixed intervals.

```php
// app/Tasks/TaskQueue.php
namespace App\Tasks;

use Luminova\Base\Queue;

class TaskQueue extends Queue 
{
    public function tasks(): ?array
    {
        return [
            [
                'handler'   => 'Luminova\Component\Seo\Sitemap::generate', // Method to execute
                'arguments' => [APP_URL],                        // Base URL of your site
                'priority'  => 5,                                // Lower = higher priority
                'forever'   => 2880                              // Run once every 2 days (in minutes)
            ]
        ];
    }
}
```

#### Running the task queue manually

```bash
php novakit task:run
```

#### Automating with cron in production

```bash
* * * * * /usr/bin/php /path/to/project/novakit task:run --flock-worker
```

> Use `--flock-worker` to prevent overlapping executions.

---

### Using a Novakit Console Command

This option use **novakit** command-line helper to execute commands similar to using **Luminova\Base\Command** but without routing attributes and it doesn't use routing system for execution making it faster in execution process. You can trigger sitemap generation on demand.

```php
// app/Console/SitemapCommand.php
namespace App\Console;

use Luminova\Base\Console as BaseConsole;
use Luminova\Component\Seo\Sitemap;
use Throwable;

class SitemapCommand extends BaseConsole
{
    protected string $name = 'Sitemap';

    // Avoid conflicts with Luminova's built-in sitemap command
    protected string $group = 'app-sitemap'; 

    protected function onCreate(): void
    {
        // Ensures CLI errors are not suppressed
        setenv('throw.cli.exceptions', 'true');
    }

    public function run(?array $options = []): int
    {
        $this->term->parse($options);
        $url = $this->term->getAnyOption('url', 'u', APP_URL);
        $command = trim($this->term->getCommand());
        
        if ($command !== 'generator') {
            return $this->term->oops($command);
        }

        try {
            if (Sitemap::generate($url, basename: 'sitemap.xml')) {
                $this->term->success('Sitemap was successfully generated.');
                return STATUS_SUCCESS; 
            }
            $this->term->error('Failed to generate sitemap.');
        } catch (Throwable $e) {
            $this->term->error('Error generating sitemap: ' . $e->getMessage());
        }

        return STATUS_ERROR;
    }
}
```

#### Register Console command

Register your sitemap command in `/bin/.novakit-console.php`

```php
// bin/.novakit-console.php
use Luminova\Command\Novakit;
use App\Console\SitemapCommand;

Novakit::command('app-sitemap', SitemapCommand::class);
```

#### Run Console command

You can run command manually or use cron in production:

```bash
php novakit app-sitemap generator --url=https://example.com
```

---

### Using Routable Base Command

Using **Routable CLI commands** via `Luminova\Base\Command`. These commands can be executed through `public/index.php` rather than the `novakit` helper in the project root.

The example below shows how to generate a sitemap using the built-in `Sitemap` generator class.

```php
// /app/Controllers/Cli/SitemapCommand.php

namespace App\Controllers\Cli;

use Luminova\Base\Command;
use Luminova\Component\Seo\Sitemap;
use Luminova\Attributes\Group;
use Luminova\Attributes\Route;
use Throwable;

#[Group('sitemap')]
class SitemapCommand extends Command
{
    /** @var string $group Group name */
    protected string $group = 'sitemap';

    protected function onCreate(): void
    {
        // Ensure CLI exceptions are thrown
        setenv('throw.cli.exceptions', 'true'); 
    }

    /**
     * Generates the sitemap for the website.
     *
     * @return int Status code: STATUS_SUCCESS or STATUS_ERROR
     */
    #[Route('generator', group: 'sitemap')]
    public function generator(): int 
    {
        try {
            $url = $this->getAnyOption('url', 'u', APP_URL);

            if (Sitemap::generate($url, basename: 'sitemap.xml')) {
                $this->success('Sitemap was successfully generated.');
                return STATUS_SUCCESS; 
            }

            $this->error('Failed to generate sitemap.');
        } catch (Throwable $e) {
            $this->error('Error generating sitemap: ' . $e->getMessage());
        }

        return STATUS_ERROR;
    }
}
```

#### Run Routable command

Navigate to your `public` directory and execute:

```bash
php index.php sitemap --url=https://example.com
```

***

### Class Definition

* Class namespace: `Luminova\Component\Seo\Sitemap`

***

## Methods

This method uses the default sitemap configuration by default, but you can provide a custom `App\Config\Sitemap` object to override settings.

### generate

Generate a sitemap or broken-link report in the `public` directory.
     
This command crawls a starting URL (or the app's default start URL) to collect all
reachable pages. It then generates either:
- an **XML sitemap** for search engines, or
- a **JSON report** of broken links (if scanning mode is enabled).

```php
public static generate(
    ?string $url = null, 
    Terminal|LazyObjectInterface|null $term = null, 
    string $basename = 'sitemap.xml',
    ?App\Config\Sitemap<Luminova\Base\Configuration> $config = null,
    array<string,mixed> $options = []
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **null&#124;string** | The start URL to scan (default: `null`).<br/>If null, the value from `env(dev.app.start.url)` will be used.  |
| `$term` | **Terminal&#124;LazyObjectInterface<Terminal>&#124;null** | Optional Terminal instance for CLI interaction (default: `null`). |
| `$basename` | **string** | Output filename e.g. `sitemap.xml` or `broken.json` (e.g, `sitemap.xml`). |
| `$config` | **Sitemap<Configuration>\|null** | Optional configuration to control crawling behavior (speed, limit, etc.). |
| `$options` | **array<string,mixed>** | Additional CLI options. |

**Return:**

- `bool` - Returns true if generation succeeds, false on failure.

**Throws:**

- `Luminova\Exceptions\RuntimeException` - If called outside CLI mode or given an invalid Terminal instance.

**Command Array Options:**

```php
bool isBroken       // Generate broken-link report instead of sitemap. Default false.
int  verbose        // Output verbosity level (0-3). Default 3.
bool isDryRun       // Simulate run without writing output. Default false.
bool ignoreAssets   // Skip assets like images, CSS, and JS. Default true.
```

**Examples:**

Generate a sitemap.

```php
use Luminova\Component\Seo\Sitemap;
use Luminova\Command\Terminal;
Sitemap::generate(
    url: 'https://example.com',
    term: new Terminal(),
    basename: 'sitemap.xml'
);
```

Generate a broken-link report
```php
use Luminova\Component\Seo\Sitemap;
use Luminova\Command\Terminal;

Sitemap::generate(
    url: 'https://example.com',
    term: new Terminal(),
    basename: 'broken.sitemap.json',
    options: ['isBroken' => true]
);
```