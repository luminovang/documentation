# Firebase Cloud Messaging

***

## Overview

Send Firebase Cloud Messaging notifications or manage channel subscriptions using the Luminova Firebase class.

***

## Introduction

Firebase Cloud Messaging (FCM) by Google is a cross-platform messaging solution that lets you reliably send messages and notifications to devices at no cost. This guide will walk you through setting up FCM to send push notifications.

The Luminova `Notification` class integrates with the `Kreait\Firebase` library and Firebase Admin SDK, enabling you to send Firebase Cloud Messaging messages to end-user devices. This can be achieved using individual device tokens, topic names, or conditional statements that match one or more topics.

To get started with the Notification module, you need to install the PHP `kreait/firebase` library. You can do this by running the following command in your terminal.

```bash
composer require "kreait/firebase-php:^7.0"
```

Additionally, you'll need to create your project in the [Firebase Developers Console](https://console.firebase.google.com/) and obtain your application's Service Account.

Please note that your usage of Firebase is subject to the [Terms of Service for Firebase Services](https://firebase.google.com/terms/).

***

* Class namespace: `\Luminova\Notifications\Notification`

***

## Initializations

Instantiate a Notification object with the specified service account JSON file.

```php
<?php
use Luminova\Notifications\Notification;

$notification = new Notification('my-service-account.json');
```

Retrieve a shared instance of the factory class using the specified service account JSON file.

```php
<?php
use Luminova\Notifications\Notification;

$factory = Notification::getFactory('my-service-account.json');
```

Alias for creating a Notification instance from factory helper function.

```php
<?php
$notification = factory('notification', 'my-service-account.json');
```

Get the factory instance directly from the factory helper function.

```php
<?php
$factory = factory('notification')->getFactory('my-service-account.json');

// OR

// Alternate syntax to retrieve the factory instance
$factory = factory('notification', 'my-service-account.json')->getFactory();
```

To explore additional examples and usage scenarios for notifications, refer to the [examples documentation](/notifications/examples.md).

***

## Methods

### constructor

Initializes the Firebase Cloud Messaging Notification class.

```php
public __construct(\Kreait\Firebase\Factory|string|array $config = 'ServiceAccount.json'): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **\Kreait\Firebase\Factory&#124;string&#124;array** | The service account filename, (e.g, JSON string, Array or Instance of Factory). |

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - If the Factory class is not found or the service account file is missing.

> If you are using service account `JSON` file, you service account json file must be stored in `/writeable/credentials/`.
> 
>If you rename it, then you must specify the filename with extension in class constructor argument `filename` (e.g, `my-service-account.json`).

**Configuration Guide**

- Filename `string` - The service account file must be stored in `/writeable/credentials/`.
- Configuration `array` - The service account configuration array.
- Configuration `string` - The service account configuration JSON string.
- Instance `Factory` - The Factory instance initialized with the preferred service account.

***

### getInstance

Initializes the Firebase Cloud Messaging Notification shared instance class.

```php
public static getInstance(\Kreait\Firebase\Factory|string|array $config = 'ServiceAccount.json'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **\Kreait\Firebase\Factory&#124;string&#124;array** | The service account filename, (e.g, JSON string, Array or Instance of Factory). |

**Return Value:**

`self` - Returns new shared instance of the notification class.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - If the Factory class is not found or the service account file is missing.

***

### getFactory

Get a shared instance of the `Kreait` Firebase Factory class, or create new factory instance from service account.
If `getFactory` is called statically without the `Notification` class initialized, it creates a factory instance from the provided service account file or configuration array.

```php
public static getFactory(string|array $account = 'ServiceAccount.json'): \Kreait\Firebase\Factory|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$account` | **string&#124;array** | The service account (e.g, filename, JSON string, an array). |

**Return Value:**

`\Kreait\Firebase\Factory|null` - Return a shared Factory instance.

**Throws:**

- [\Luminova\Exceptions\RuntimeException](/exceptions/classes.md#runtimeexception) - If the Factory class is not found or the service account file is missing.

**Configuration Guide**

- Filename `string` - The service account file must be stored in `/writeable/credentials/`.
- Configuration `array` - The service account configuration array.
- Configuration `string` - The service account configuration JSON string.

***

### send

To send a notification message to a specific device by token.

```php
public send(\Luminova\Notifications\Models\Message|array<string,mixed> $config, bool $validateOnly = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **Message<array<string,mixed>** | The notification payload from array or Message instance. |
| `$validateOnly` | **bool** | Optional. If set to true, the message will only be validated without sending. |

**Return Value:**

`self` - Return instance of luminova firebase notification class.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - If token is not valid or an error occurred while sending notification.

***

### channel

To send a notification message to a specific topic name.

```php
public channel(\Luminova\Notifications\Models\Message|array<string,mixed> $config, bool $validateOnly = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **Message<array<string,mixed>** | The notification payload from array or Message instance. |
| `$validateOnly` | **bool** | Optional. If set to true, the message will only be validated without sending. |

**Return Value:**

`self` - Return instance of luminova firebase notification class.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - If topic is not valid or an error occurred while sending notification.

***

### condition

To send conditional notification message, by specifying an expression the target topics, this allows you to send a message to a combination of topics by specifying a conditions like `'TopicA' in topics && ('TopicB' in topics || 'TopicC' in topics)`.

```php
public condition(\Luminova\Notifications\Models\Message|array<string,mixed> $config, bool $validateOnly = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **Message<array<string,mixed>** | The notification payload from array or Message instance. |
| `$validateOnly` | **bool** | Optional. If set to true, the message will only be validated without sending. |

**Return Value:**

`self` - Return instance of luminova firebase notification class.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - If the topic is not provided correctly.

***

### broadcast

To send notifications to multiple devices by tokens.

```php
public broadcast(\Luminova\Notifications\Models\Message|array<string,mixed> $config, bool $validateOnly = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **Message<array<string,mixed>** | The notification data. |
| `$validateOnly` | **bool** | Optional. If set to true, the message will only be validated without sending. |

**Return Value:**

`self` - Return instance of luminova firebase notification class.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - If tokens are not provided or if an error occurs during message construction.

***

### subscribe

To subscribe a single device token to a specific topic.

```php
public subscribe(string $token, string $topic): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$token` | **string** | The device token. |
| `$topic` | **string** | The topic to subscribe to. |

**Return Value:**

`self` - Return instance of luminova firebase notification class.

***

### subscribers

To subscribe multiple device tokens to list of topics.

```php
public subscribers(array<int,string> $topics, array<int,string> $tokens): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$topics` | **array<int,string>** | The device tokens. |
| `$tokens` | **array<int,string>** | The topics to subscribe. |

**Return Value:**

`self` - Return instance of luminova firebase notification class.

***

### unsubscribe

To unsubscribe device token from a specific topic.

```php
public unsubscribe(string $token, string $topic): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$token` | **string** | The device token. |
| `$topic` | **string** | The topic to unsubscribe from. |

**Return Value:**

`self` - Return instance of luminova firebase notification class.

***

### unsubscribers

To unsubscribe multiple device tokens from list topics.

```php
public unsubscribers(array<int,string> $topics, array<int,string> $tokens): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$topics` | **array<int,string>** | The topic to unsubscribe from. |
| `$tokens` | **array<int,string>** | The tokens to unsubscribe from list of topics. |

**Return Value:**

`self` - Return instance of luminova firebase notification class.

***

### desubscribe

To unsubscribe list of device token from all topics.

```php
public desubscribe(array<int,string> $tokens): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tokens` | **array<int,string>** | The device tokens to unsubscribe from all topics. |

**Return Value:**

`self` - Return instance of luminova firebase notification class.

***

### isDone

Determine if notification or topic management method task was completed successfully.

```php
public isDone(): bool
```

**Return Value:**

`bool` - Return true if successful, false otherwise.

***

### getReport

To retrieve response report from firebase `Kreait` notification or topic management method call.

```php
public getReport(): \Kreait\Firebase\Messaging\MulticastSendReport|array
```

**Return Value:**

`\Kreait\Firebase\Messaging\MulticastSendReport|array` - The response from Firebase Cloud Messaging.