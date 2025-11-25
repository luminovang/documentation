# OpenAI Models Integration

***

## Overview

Using Luminova OpenAI class, to integrate AI functionalities into your applications, enhancing the capabilities and providing users with advanced AI-powered features.

***

## Introduction

The Luminova `OpenAI` class provides an easy to use methods for integrating OpenAI's powerful language models into your applications. This class abstracts the complexities of interacting with OpenAI's API, enabling developers to effortlessly harness the capabilities of models like `ChatGPT`, `DALL-E`, `Whisper`, and others. Whether you're building chatbots, generating images, performing text analysis, or converting speech, the `OpenAI` class offers a useful methods to embedding advanced AI functionalities into your projects. To learn more about [OpenAI Integration](https://platform.openai.com/docs/api-reference).

### Example Usage

Here's a basic example demonstrating how to use the `Luminova\OpenAI` class to generate text completions:

```php
use Luminova\Utility\Ai\Models\OpenAI;
use Luminova\Exceptions\AppException;

$openai = new OpenAI('your-api-key');

try {
    $options = ['model' => 'gpt-3.5-turbo-instruct'];
    $response = $openai->completion('Tell me a joke.', $options);
    print_r($response);
} catch (AppException $e) {
    echo 'Error: ' . $e->getMessage();
}
```

Using the AI `Model` class for easy implementation, this is useful when you want to switch `AIs`.

```php
use Luminova\Utility\Ai\Model;
use Luminova\Utility\Ai\Models\OpenAI;
use Luminova\Exceptions\AppException;

$ai = new Model(); // Optional pass AI class instance (default: OpenAI)

try {
    $options = ['model' => 'gpt-3.5-turbo-instruct'];
    $response = $ai->completion('Tell me a joke.', $options);
    print_r($response);
} catch (AppException $e) {
    echo 'Error: ' . $e->getMessage();
}
```

***

* Class namespace: `Luminova\Utility\Ai\Models\OpenAI`
* This class implements:
[Luminova\Interface\AiInterface](/interface/classes.md#aiinterface), [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

***

## Methods
To get started, [Set up your API key](/configs/ai-models.md), and optional organization and project IDs.

### constructor

Constructs a new instance.

```php
public __construct(string $apiKey, string $version = 'v1', string|null $organization = null, string|null $project = null)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$apiKey` | **string** | The API key for OpenAI. |
| `$version` | **string** | The API version (default: `v1`). |
| `$organization` | **string&#124;null** | The organization ID (optional). |
| `$project` | **string&#124;null** | The project ID (optional). |

***

### completion

Get completions for the given prompt.

```php
public completion(string $prompt, array $options = []): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prompt` | **string** | The prompt for which completions are requested. |
| `$options` | **array** | Optional parameters to pass to the API.<br />Required options:<br />- `model` (string): The model to use for completion (default: `gpt-3.5-turbo-instruct`). |

**Return Value:**
`array` - Returns an array of completion choices.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### suggestions

Get suggestions for the given prompt.

```php
public suggestions(string $prompt, array $options = []): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prompt` | **string** | The prompt for which suggestions are requested. |
| `$options` | **array** | Optional parameters to pass to the API.<br />Required options:<br />- `model` (string): The model to use for suggestions (default: `gpt-3.5-turbo-instruct`). |

**Return Value:**
`array` - Returns an array of suggestion choices.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### message

Send a message and receive replies.

```php
public message(string $prompt, array $options = []): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prompt` | **string** | The message prompt to send. |
| `$options` | **array** | Optional parameters to pass to the API.<br />Required options:<br />- `model` (string): The model to use for the message (default: `gpt-3.5-turbo-instruct`). |

**Return Value:**
`array` - Returns an array of replies.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### image

Generate random images.

```php
public image(string $prompt, array $options = []): array|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prompt` | **string** | The message prompt to send. |
| `$options` | **array** | Optional parameters to pass to the API.<br />Required options:<br />- `model` (string): The model to use for image generation (default: `dall-e-3`).<br />- `response_format` (string): The format of the generated image (default: `url`). |

**Return Value:**
`array|false` - An array of image URLs or base64 encoded images, false on failure.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### imageEdit

Edit or swap areas of an image with another image.

```php
public imageEdit(string $prompt, array $options): array|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$prompt` | **string** | The message prompt to send. |
| `$options` | **array** | Optional parameters to pass to the API.<br />Required options:<br />- `model` (string): The model to use for image editing (default: `dall-e-2`).<br />- `image` (string): The path to the original image to edit (PNG only, max 4MB).<br />- `mask` (string): The path to the image to swap areas with (PNG only, max 4MB).<br />- `response_format` (string): The format of the edited image (default: `url`). |

**Return Value:**
`array|false` - An array of edited image URLs or base64 encoded images, false on failure.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### fineTune

Create additional training on how `ChatGPT` should behave based on your training examples.

```php
public fineTune(string $trainingFile, array $options = []): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$trainingFile` | **string** | The path to the training file. |
| `$options` | **array** | Optional parameters to pass to the API.<br />Required options:<br />- `model` (string): The model to use for fine-tuning (default: `gpt-3.5-turbo`). |

**Return Value:**
`array` - Returns an array of training responses.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### embed

Get text embeddings to measure the relatedness of text strings.

```php
public embed(string|array $input, array $options = []): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$input` | **string&#124;array** | The text or array of text to embed. |
| `$options` | **array** | Optional parameters to pass to the API.<br />Required options:<br />- `model` (string): The model to use for embeddings (default: `text-embedding-ada-002`). |

**Return Value:**
`array` - Returns an array of embedding vectors.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### speech

Generate audio from text.

```php
public speech(string $text, array $options = []): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$text` | **string** | The text to convert to audio. |
| `$options` | **array** | Additional options for conversion.<br />Available options:<br />- `model` (string): The model to use for conversion (default: `tts-1`).<br />- `voice` (string): The voice to use for conversion (default: `alloy`), available voices: `alloy`, `echo`, `fable`, `onyx`, `nova`, or `shimmer`.<br />- `path` (string): The destination path to save the converted file (default: `writeable/ai/speech`).<br />- `response_format` (string): The format of the converted file (default: `mp3`).<br />- `symlink` (string): Optional symbolic link destination. |

**Return Value:**
`string|false` - Return file url on success, false on failure.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### audio

Translates audio to text.

```php
public audio(string $filename, array $options = []): string|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string** | The path to the audio file. |
| `$options` | **array** | Additional options for conversion.<br />Available options:<br />- `model` (string): The model to use for conversion (default: `whisper-1`).<br />- `response_format` (string): The format of the converted file (default: `json`). |

**Return Value:**
`string|false` - Returns the converted audio on success, false on failure.

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### models

Retrieve a list of available models.

```php
public models(string|null $name = null): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | The name of the model to retrieve (optional). |

**Return Value:**
`array` - An array containing information about the model(s).

**Throws:**

- [Luminova\Exceptions\RuntimeException](/error-handlers/exceptions.md#runtimeexception) - If an error is encountered during the network request.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error is encountered during JSON decoding.

***

### API Options

#### Shared Options

- `model` (string) (required) - The model to use for the API call.
- `n` (int) (optional) - The number of items to generate.
- `user` (string) (optional) - The identifier for the end-user.

### Conversions

- `max_tokens` (int) (optional) - The maximum number of tokens to generate.
- `temperature` (float) (optional) - The sampling temperature to use, ranging from `0` to `1`.

### Image Generation

- `size` (string) (optional) - The dimensions of the generated image, e.g., `256x256`.
- `response_format` (string) (optional) - The format of the API response, e.g., `url` or `base64_json`.

### Image Editing

- `image` (string) (required) - The path to the original image to edit (PNG only, max 4MB).
- `mask` (string) (required) - The path to the image to use as a mask (PNG only, max 4MB).
- `size` (string) (optional) - The dimensions of the edited image, e.g., `256x256`.
- `response_format` (string) (optional) - The format of the API response, e.g., `url` or `base64_json`.

### Speech Generation

- `voice` (string) (optional) - The voice to use for audio conversion. Available voices: `alloy`, `echo`, `fable`, `onyx`, `nova`, or `shimmer`. Default is `alloy`.
- `path` (string) (optional) - The destination path to save the converted file. Default is `writeable/ai/speech`.
- `symlink` (string) (optional) - The optional symbolic link destination for audio files.
- `response_format` (string) (optional) - The format of the API response, e.g., `mp3`, `wav`.

### Audio Transcription

- `prompt` (string) (optional) - The optional text prompt to guide the transcription.
- `language` (string) (optional) - The language of the audio file.
- `response_format` (string) (optional) - The format of the API response, e.g., `json`, `text`.
- `temperature` (float) (optional) - The sampling temperature to use for transcription, ranging from `0` to `1`.

### Fine-Tuning

- `validation_file` (string) (required) - The path to the validation file for fine-tuning.
- `training_file` (string) (required) - The path to the training file for fine-tuning.

### Embedding

- `dimensions` (int) (optional) - The number of dimensions for the embedding vector.
- `encoding_format` (string) (optional) - The format of the embedding, e.g., `json`, `binary`.