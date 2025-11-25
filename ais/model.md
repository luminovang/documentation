# Integrating Artificial Intelligence Models

***

## Overview

The AI Model class simplifies the interaction with different AI models by handling initialization and configuration, ensuring a more efficient workflow for developers.

***

## Introduction

The `AI Model` class, allows easy interaction with different AI models interchangeably without the need to manually initialize the `AI` class and pass API keys every time. It leverages the AI configuration class `app\Config\AI` to manage these settings.

***

* Class namespace: `Luminova\Utility\Ai\Model`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***

## Methods

### constructor

Initialize model instance with AI interface.

```php
public __construct(Luminova\Interface\AiInterface $ai = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ai` | **Luminova\Interface\AiInterface** | The AI object to use (default: `OpenAI`). |

***

### getAiModel

Retrieves the instance of the AI interface.

```php
public getAiModel(): Luminova\Interface\AiInterface
```

**Return Value:**
`Luminova\Interface\AiInterface` - Return the AI object.