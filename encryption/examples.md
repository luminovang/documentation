# Encryption Examples

***

## Overview

A Comprehensive Guide to the luminova's Encryption Interface Class for PHP Framework Integration with OpenSSL and Sodium Libraries.

***

## Introduction

### Crypter

Before using Luminova's Crypter, ensure that your application key is properly configured in the environment file `app.key`. 
It is recommended to utilize the `php novakit generate:key` command for generating a new key when necessary. 
By default, an application key is generated during the installation of Luminova.

Furthermore, you can configure additional optional settings in `/app/Controllers/Config/Encryption.php`, such as the encryption handler class, encryption mode, hash digest, etc.

#### Encrypting

This section describes how to encrypt data using the Crypter class.

```php
<?php
use \Luminova\Security\Crypter;
use \Luminova\Exceptions\EncryptionException;
use PHPUnit\TextUI\XmlConfiguration\CodeCoverage\Report\Php;

/**
 * Store a User token.
 */
public function addToken(): void
{
	try{
		$id = escape($this->request->getPost('user_id'));
		$tbl =  $this->query->table('user_tokens');
		$tbl->insert([
			'user_id' => $id,
			'token' => Crypter::encrypt($this->request->getPost('token')),
		]);

		response(200)->json(['status' => 'ok']);
	}catch(EncryptionException $e){
		// ...
	}
}
```

#### Decrypting

This section describes how to decrypt data using the Crypter class.

```php
<?php
/**
 * Retrieve a User token.
 */
public function useToken(): void
{
	try{
		$id = escape($this->request->getPost('user_id'));
		$tbl =  $this->query->table('user_tokens');
		$tbl->where('user_id', '=', $id);
		$encrypted = $tbl->find(['token']);

		$token = Crypter::decrypt($encrypted);

		response(200)->json(['status' => 'ok', 'token' => $token]);
	}catch(EncryptionException $e){
		// ...
	}
}
```

### Single Encryption Class Usage

If you're looking to encrypt data in your applications, you have the option to choose between two encryption classes provided by the Luminova framework: `OpenSSL` and `Sodium`. 
These classes offer different approaches to encryption, each with its own strengths and benefits. Let's take a closer look at how you can use OpenSSL and Sodium effectively for securing your sensitive data.

#### OpenSSL

The OpenSSL class provides methods for encrypting and decrypting data using OpenSSL encryption.

```php 
use \Luminova\Security\Encryption\OpenSSL;

// Initialize the encryption key and message
$key = "my-encryption-key";
$message = "hello this is awesome secret";

// Create an instance of OpenSSL
$crypt = new OpenSSL($key);

// Set a nonce (initialization vector)
$crypt->setNonce();

// Set the data to be encrypted
$crypt->setData($message);

// Encrypt the data
$encrypted = $crypt->encrypt();

// Output the encrypted message
echo "Encrypted message: " . $encrypted;

// Decrypt the encrypted message
$crypt->setKey($key); // Set the key for decryption
$crypt->setData($encrypted); // Set the encrypted data
echo "Decrypted message: " . $crypt->decrypt();
```

#### Sodium

The Sodium class provides methods for encrypting and decrypting data using Sodium encryption.

```php 
use \Luminova\Security\Encryption\Sodium;

// Initialize the encryption key and message
$key = "my-encryption-key";
$message = "hello this is awesome secret";

// Create an instance of Sodium
$crypt = new Sodium($key);

// Set a nonce (initialization vector)
$crypt->setNonce();

// Set the data to be encrypted
$crypt->setData($message);

// Encrypt the data
$encrypted = $crypt->encrypt();

// Output the encrypted message
echo "Encrypted message: " . $encrypted;

// Decrypt the encrypted message
$crypt->setKey($key); // Set the key for decryption
$crypt->setData($encrypted); // Set the encrypted data
echo "Decrypted message: " . $crypt->decrypt();
```