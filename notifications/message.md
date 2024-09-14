# Notification Service Message Model

***

## Overview

Build Firebase notification message payload configurations using the Luminova Message model.

***

## Introduction

The notification `Message` model enables you to build a notification payload efficiently. It provides various methods to add custom fields to the notification payload, to customize your application requirements and platform configuration settings.

***

* Class namespace: `\Luminova\Notifications\Models\Message`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***

## Constants

This constants represent platform specific notifications configuration.

| Constant | Visibility | Type | Value |
|:---------|:-----------|:-----|:------|
|`DEFAULT`|public|int|1|
|`ANDROID`|public|int|2|
|`APN`|public|int|3|
|`WEBPUSH`|public|int|4|

## Methods

#### Example Usage

```php
$message = new Message();
$message->setPlatform(Message::ANDROID)
  ->setRaw(false)
  ->setToken('your_single_notification_token')
  ->setTopic('your_topic')
  ->setTokens(['token1', 'token2'])
  ->add('foo', 'value', 'android')
  ->addNested('foo.bar.baz', 'baz value')
  ->addData('key', 'value1')
  ->addAndroid('key', 'value')
  ->addApns('key' => 'value')
  ->addWebpush(['key' => 'value'])
  ->setFcmOptions(['key' => 'value'])
  ->setNotification([
      'title' => 'Your Notification Title',
      'body' => 'Your Notification Message',
      'image' => 'https://example.com/image.png'
  ]);
```

### constructor

Initialize new message model, you can optionally pass an array to build your notification payload from.

```php
public __construct(array|null $setter = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$setter` | **array&#124;null** | An optional array to initialize model from. |

**Setter Fields**

- platform (int) Notification specific platform (default: 1).
- raw `bool` - Send notification from your build payload or array without modifying.
- token `string` - Single notification token, (required) if you are sending notification to a single device.
- topic `string` - Single notification topic, (required) if you are sending notification to a topic/channel.
- tokens `array<int,string>` -  Multiple notification tokens, (required) if you are sending notification to multiple device tokens.
- data `array<string,mixed>` - Optional data to send with the notification.
- android `array<string,mixed>` - Android specific configuration.
- apns `array<string,mixed>` - APNs specific configuration.
- webpush `array<string,mixed>` - WebPush specific configuration.
- fcm_options `array<string,mixed>` - Optional firebase configurations.
- notification `array<string,mixed>` - Notification payload information.
  - - title (string) Notification title.
  - - body (string) Notification message body.
  - - image (string) Notification image URL.

**Setter Fields Array Example**

```php
<?php 
$setter = [
  'platform' => 1, // Default for all platforms
  'raw' => false, // Default no raw payload
  'token' => 'your_single_notification_token',
  'topic' => 'your_topic',
  'tokens' => ['token1', 'token2'],
  'data' => ['key1' => 'value1', 'key2' => 'value2'],
  'android' => [
    'key' => 'value'
  ],
  'apns' => [
    'key' => 'value'
  ],
  'webpush' => [
    'key' => 'value'
  ],
  'fcm_options' => [
    'key' => 'value'
  ],
  'notification' => [
    'title' => 'Your Notification Title',
    'body' => 'Your Notification Message',
    'image' => 'https://example.com/image.png'
  ]
];
```

**See Also:**

* [Learn more about Notification fields](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages#Notification)
* [ Learn more about APNs specific configurations](https://firebase.google.com/docs/cloud-messaging/admin/send-messages#apns_specific_fields).
* [Learn more about Android specific configurations](https://firebase.google.com/docs/cloud-messaging/admin/send-messages#android_specific_fields)
* [Learn more about WebPush specific configurations](https://firebase.google.com/docs/cloud-messaging/admin/send-messages#webpush_specific_fields)

***

### add

The add method allows you to add key-value pairs to the notification payload. It supports nested payload structures and can merge values recursively if needed. If the root key is specified, the method ensures that the payload under the root is an array before adding or merging the key-value pair.

```php
public add(string $key, mixed $value, string|null $root = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to add. |
| `$value` | **mixed** | The value to associate with the key. |
| `$root` | **string&#124;null** | Optional root key for nested payloads, if `NUll`, the key will be store in payload root instead. and replace any existing key value. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

**Example**

```php
<?php
use \Luminova\Notifications\Models\Message;

$message = new Message();

// Add top-level key-value pair
$message->add('title', 'New Message');

// Add nested key-value pair
$message->add('body', 'You have a new message', 'notification');

// Add and merge array values
$message->add('data', ['key1' => 'value1'], 'extra');
$message->add('data', ['key2' => 'value2'], 'extra');
```

Example payload after additions

```php
[
    'title' => 'New Message',
    'notification' => [
        'body' => 'You have a new message'
    ],
    'extra' => [
        'data' => [
            'key1' => 'value1',
            'key2' => 'value2'
        ]
    ]
]
```

***

### addNested

Add a nested configuration key-value pair to the payload using dot (.) notation string as a delimiter to represent the nested structure of keys.

The `addNested` method is designed to enhance the addition of values to deeply nested structures within the payload. It parses a dot notation string to determine the nested keys and sets the provided value at the correct depth in the payload.

```php
public addNested(string $keys, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keys` | **string** | The dot-separated keys representing the nested structure. |
| `$value` | **mixed** | The value to set at the specified nested location. It can be of any type (string, array, etc.). |

**Return Value:**

`self` - Return notification message model instance for method chaining. Returns the updated instance of the class, allowing method chaining.

**Example**

Add a value to a deeply nested structure using dot notation.

```php
<?php
use \Luminova\Notifications\Models\Message;

$message = new Message();
$message->addNested('notification.alerts.urgent', 'Immediate attention required');
```
Example payload after addition.

```php
[
  'notification' => [
      'alerts' => [
          'urgent' => 'Immediate attention required'
      ]
  ]
];
```
Chain multiple additions

```php
$message->addNested('notification.alerts.low', 'Routine checkup')
  ->addNested('notification.alerts.medium', 'Scheduled maintenance');
```
Example payload after chaining additions.

```php
[
  'notification' => [
      'alerts' => [
          'urgent' => 'Immediate attention required',
          'low' => 'Routine checkup',
          'medium' => 'Scheduled maintenance'
      ]
  ]
];
```

***

### addApns

Add APNs specific configuration key-value pair to notification payload.

```php
public addApns(string $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to add. |
| `$value` | **mixed** | The value to associate with the key. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### addWebpush

Add WebPush specific configuration key-value pair to the notification payload.

```php
public addWebpush(string $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to add. |
| `$value` | **mixed** | The value to associate with the key. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### addAndroid

Add Android specific configuration key-value pair to the notification payload.

```php
public addAndroid(string $key, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to add. |
| `$value` | **mixed** | The value to associate with the key. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### addData

Add a custom key-value pair to notification data object.

```php
public addData(string $key, string $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to add. |
| `$value` | **string** | The value to associate with the key. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### addNotification

Add a key-value pair to the notification object.

```php
public addNotification(string $key, string $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to add. |
| `$value` | **string** | The value to associate with the key. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setNotification

Set array of key-value pair to the notification object.

```php
public setNotification(array&lt;string,mixed&gt; $notification): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$notification` | **array<string,mixed>** | The notification payload object. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setFcmOptions

Set FCM options. array of key-value pair to the `fcm_options` object.

```php
public setFcmOptions(array&lt;string,mixed&gt; $options): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$options` | **array<string,mixed>** | The FCM options. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setTitle

Set the display title for notification.

```php
public setTitle(string $title): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$title` | **string** | The notification title. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setBody

Set the display body for notification.

```php
public setBody(string $body): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$body` | **string** | Notification message body. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setImageUrl

Set the image URL for notification.

```php
public setImageUrl(string $url): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The image url to set. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setIcon

Set the icon for the notification.

```php
public setIcon(string $icon): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$icon` | **string** | The notification icon. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setSound

Set the sound for the notification.

```php
public setSound(string $sound): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$sound` | **string** | Notification sound. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setVibration

Set the vibration pattern for the notification.

```php
public setVibration(array $vibrate): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$vibrate` | **array** | The vibrate pattern e.g. [200, 100, 200]. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setTag

Set a tag for the notification.

```php
public setTag(string $tag): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tag` | **string** | The notification tag. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setColor

Set a color for the notification.

```php
public setColor(string $color): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$color` | **string** | The notification color. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setAnalytic

Set the analytic label for the notification.

```php
public setAnalytic(string $analytic): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$analytic` | **string** | Set analytic label. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setPriority

Set the notification priority.

```php
public setPriority(string $priority): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$priority` | **string** | The notification priority (e.g normal). |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setTtl

Set TTL for the notification.

```php
public setTtl(string $ttl): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ttl` | **string** | The ttl (e.g. 3600s). |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setLink

Set a link to open when notification is clicked.

```php
public setLink(string $url): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The notification action url. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setClickAction

Set click action, an activity with a matching intent filter is launched when a user clicks on the notification.

```php
public setClickAction(string $action): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$action` | **string** | The notification intent action. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setBadgeCount

Sets the number of badge count this notification will add.

```php
public setBadgeCount(int $count): self
```

This may be displayed as a badge count for launchers that support badging.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$count` | **int** | The number of badge to add for this notification. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setPackage

Sets package restriction, the package name of your application where the registration token must match in order to receive the message.

```php
public setPackage(string $package): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$package` | **string** | The notification package restriction (e.g: com.app.name.foo). |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### isRaw

Determine if notification should be sent raw from payload array.

```php
public isRaw(): bool
```

**Return Value:**

`bool` - Return true if should send notification as raw, otherwise false.

***

### setRaw

Set raw flag, to send notification raw from payload array, instead of building notification.

```php
public setRaw(bool $raw): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$raw` | **bool** | Should send notification as raw, otherwise false. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setTopic

Set the notification topic to use when called `channel` method.

```php
public setTopic(string $topic): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$topic` | **string** | The notification topic name. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setConditions

Set the notification topic conditional expression to use when called `condition` method.

```php
public setConditions(string $conditions): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$conditions` | **string** | The conditional expression. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setTokens

Set the notification device tokens to use when called `broadcast` method.

```php
public setTokens(array&lt;int,string&gt; $tokens): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tokens` | **array<int,string>** | The device notification tokens. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setToken

Set the notification device token to use when called `send` method.

```php
public setToken(string $token): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$token` | **string** | The notification device token. |

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### setPlatform

Set the notification platform type.

```php
public setPlatform(int $platform): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$platform` | **int** | The notification platform. |

**Expected Platforms**

- Message::DEFAULT `1` - Default notification without platform specific.
- Message::ANDROID `2` - Android platform.
- Message::APN `3` - APNs platform.
- Message::WEBPUSH `4` - WebPush platform.

**Return Value:**

`self` - Return notification message model instance for method chaining.

***

### getTokens

Get the array of notification device tokens.

```php
public getTokens(): array
```

**Return Value:**

`array` - Return the array of notification device tokens.

***

### getConditions

Get notification topic conditional expression.

```php
public getConditions(): string
```

**Return Value:**

`string` - Return notification topic expression.

***

### getToken

Get notification token.

```php
public getToken(): string
```

**Return Value:**

`string` - Return notification token.

***

### getPlatform

Get notification platform id.

```php
public getPlatform(): int
```

**Return Value:**

`int` - Returns the notification platform id.

***

### getPriority

Get notification priority.

```php
public getPriority(): string
```

**Return Value:**

`string` - Return notification priority.

***

### getTitle

Get notification title.

```php
public getTitle(): string
```

**Return Value:**

`string` - Return notification title.

***

### getBody

Get notification body.

```php
public getBody(): string
```

**Return Value:**

`string` - Return notification body.

***

### getData

Get notification custom data.

```php
public getData(): array&lt;string,mixed&gt;
```

**Return Value:**

`array&lt;string,mixed&gt;` - Returns the notification data.

***

### getImageUrl

Get notification image url.

```php
public getImageUrl(): string
```

**Return Value:**

`string` - Return notification image url.

***

### getTopic

Get notification channel topic.

```php
public getTopic(): string
```

**Return Value:**

`string` - Returns notification channel topic.

***

### getAnalytic

Get notification analytic label.

```php
public getAnalytic(): string
```

**Return Value:**

`string` - Return notification analytics label.

***

### get

Get key from notification object.

```php
public get( $key,  $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **** | Key to retrieve. |
| `$default` | **** | Default value. |

**Return Value:**

`mixed` - Return notification key value.

***

### getPayload

Get notification payload or a specific key from payload.

```php
public getPayload(string $key = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | Optional key to retrieve from payload. |

**Return Value:**

`mixed` - Return array of notification payload or value from passed key.

***

### fromArray

Process notification payload and return an array representing full notification configurations.

```php
public fromArray(): array&lt;string,mixed&gt;
```

**Return Value:**

`array&lt;string,mixed&gt;` - Return notification payload.