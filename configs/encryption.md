# Encryption and Decryption Configurations

***

## Overview

The encryption configuration properties provide flexibility and control over the encryption and decryption of data in your application.

***

## Introduction

The `Encryption Configuration` class provides properties to configure encryption settings for data encryption and decryption in your applications. These properties allow developers to specify the encryption algorithm, method, digest, and key information used for encryption and decryption operations.

***

* Class namespace: `\App\Config\Encryption`
* File path: `/app/Config/Encryption.php`
* This class is marked as **final** and can't be subclassed

***

## Properties

### handler

Specify encryption algorithm to use (openssl or sodium).

```php
public string $handler = 'openssl';
```

***

### method

The encryption mode for openssl (e.g., 'AES-128-CBC', 'AES-192-CBC', 'AES-128-CFB', 'AES-128-ECB').

```php
public string $method = 'AES-128-CBC';
```

***

### digest

Specify hashing digest to use (e.g., SHA512, SHA256).

```php
public string $digest = 'SHA512';
```

***

### keyInfo

Provides information about the key used in openssl encryption.

```php
public string $keyInfo = '';
```
