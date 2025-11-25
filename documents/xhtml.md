# XML and XHTML Markups Builder

***

## Overview

The XML and XHTML Markup Builder is a flexible tool for generating well-structured XML and XHTML elements dynamically within your application.

***

## Introduction

When generating dynamic `XML` or `HTML` for the frontend, the `Xhtml` (Extensible Hypertext Markup Language) builder class in Luminova simplifies the process with static methods to create markup elements. It enables you to programmatically build tags, attributes, and content with clean, efficient syntax, ensuring compliance with XML and XHTML standards. This includes enforcing case sensitivity, proper nesting, and closing all tags (even self-closing ones). Tag names are automatically converted to lowercase, making the class ideal for applications that require strict document formatting, such as web pages, feeds, or data exchanges.

For detailed instructions on generating elements, refer to the [XHTML Examples Documentation](/examples/xhtml-builder.md) to learn how to use the class for creating various HTML elements.

***

## Class Definition

* Class namespace: `Luminova\Component\Widget\Xhtml`
* Parent class: [Luminova\Component\Widget\Document](/documents/base-builder.md)

***

## Properties Configuration

See the [Base Document Builder Helper Class](/documents/base-builder.md) for more information relating to properties configuration.

***

### template

The document element style to use (e.g, `Xhtml::HTML5`, `Xhtml::BOOTSTRAP5`).

```php
Xhtml::$template = Xhtml::HTML5;
```

***

### encoding

The encoding to use for escaping document element, contents and attributes.

```php
Xhtml::$encoding = 'UTF-8'
```

***

### xhtmlStrictTagNames

Determines whether element tag names should be automatically converted to lowercase (in strict XHTML mode) or preserved as they were passed, which can be useful for applications using custom template elements or case-sensitive tags (such as when working with XML-like data structures).

**Usage Examples:**

```php
Xhtml::$xhtmlStrictTagNames = true;

echo Xhtml::element('MyCustomTag', 'Content'); 
// Generates: <mycustomtag>Content</mycustomtag>
```

```php
Xhtml::$xhtmlStrictTagNames = false;

echo Xhtml::element('MyCustomTag', 'Content'); 
// Generates: <MyCustomTag>Content</MyCustomTag>
```

***

## Methods

### __callStatic

Dynamically generates an HTML element using static method calls.

This method allows calling methods that are not explicitly defined in the class.
The method name is converted to lowercase and the arguments are unpacked into relevant parameters for the `element` method.

```php
public static __callStatic(string $method, array $arguments): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$method` | **string** | The name of the method being called, converted to lowercase to use as HTML tag name. |
| `$arguments` | **array** | The arguments passed to the method.<br />The arguments are unpacked as follows:<br />- string $content The string content to be passed to the element (default: '').<br />- bool $closeElement Whether the element should be self-closing (default: false).<br />- array<string,string> $attributes Additional HTML attributes for the element. |

**Return Value:**

`string` - Returns the generated HTML element as a string.

**Example usage:**

```php
echo Xhtml::div('Content', ['class' => 'my-class']);
echo Xhtml::p('Paragraph content', ['id' => 'my-paragraph']);
```

***

### element

Generates an HTML element with optional attributes and content.

This method creates a specified HTML tag, with the option to include
attributes and content. The tag can either be a block element (with opening and closing tags) or a self-closing tag.

```php
public static element(string $tag, ?string $content = null, bool $closeElement = true, array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tag` | **string** | The HTML tag to be generated (e.g., 'div', 'span', 'p'). |
| `$content` | **string&#124;null** | Optional content to be placed inside the element (default: ''). |
| `$closeElement` | **bool** | Whether to close the tag with content or self-close it (default: true). |
| `$attributes` | **array** | Optional associative array of attributes to be added<br />to the HTML tag (e.g., ['class' =&gt; 'my-class', 'id' =&gt; 'my-id']). |

**Return Value:**

`string` - Returns the generated HTML element as a string.

**Usage Examples:**

Generate a div with content.

```php
echo Xhtml::element('div', 'Hello, World!', true, ['class' => 'greeting']);
```
**Output:** 
```html 
<div class="greeting">Hello, World!</div>
```

Generate a self-closing image tag.

```php
echo Xhtml::element('img', null, false, ['src' => 'image.jpg', 'alt' => 'My Image']);
```
**Output:** 

```html 
<img src="image.jpg" alt="My Image" />
```

***

### elements

Generates multiple HTML elements based on an array of element specifications.

Each element in the array should be an associative array containing the following keys:

- **tag** (string): The HTML tag to generate (e.g., 'div', 'p', 'span').
- **content** (string): The content to be placed inside the HTML tag (default: empty string).
- **closeElement** (bool): Whether to close the tag as a block element (default: true if content is provided).
- **attributes** (array): Optional associative array of HTML attributes for the tag (default: empty array).

```php
public static elements(array $elements): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$elements` | **array** | Array of elements where each element is defined with keys ('tag', 'content', 'closeElement', and 'attributes'). |

**Return Value:**

`string` - Returns the generated HTML elements as a string.

**Usage Examples:**

```php
$elements = [
    ['tag' => 'div', 'content' => 'Hello, World!', 'closeElement' => true, 'attributes' => ['class' => 'greeting']],
    ['tag' => 'p', 'content' => 'This is a paragraph.', 'attributes' => ['style' => 'color: blue;']],
    ['tag' => 'span', 'content' => 'Highlighted text', 'closeElement' => false, 'attributes' => ['class' => 'highlight']]
];

echo Xhtml::elements($elements);
```
**Output:**

```html
<div class="greeting">Hello, World!</div>
<p style="color: blue;">This is a paragraph.</p>
<span class="highlight">Highlighted text</span>
```

***

### tags

Generates multiple non-self-closing HTML elements of a specific tag, based on an array of element attributes.

Each element attribute should be an associative array containing the
attributes to apply to the generated element.

```php
public static tags(string $tag, array<int,array<string,mixed>> $attributes): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tag` | **string** | The HTML tag to generate (e.g., 'div', 'p', 'span'). |
| `$attributes` | **array<int,array<string,mixed>>** | Array of element attributes,<br />where each entry is an associative array of attributes for a single element. |

**Return Value:**

`string` - Returns the generated HTML elements as a string.

**Usage Examples:**
```php
$attributes = [
    ['class' => 'item', 'data-id' => '1'],
    ['class' => 'item', 'data-id' => '2'],
    ['class' => 'item', 'data-id' => '3']
];

echo Xhtml::tags('hr', $attributes);
```
**Output:**

```html 
<hr class="item" data-id="1" />
<hr class="item" data-id="2" />
<hr class="item" data-id="3" />
```

***

### comment

Generate an inline comment block or conditional comment with the given content.

This method creates a conditional comment for Internet Explorer based on the provided conditions. If no conditions are specified, it generates a standard HTML comment. Optionally, the comment can be marked as hidden by appending `//` at the end.

```php
public static comment(string $content, bool $hide = false, ?string $conditions = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The content to include within the comment. |
| `$hide` | **bool** | Enable to hide scripts from browsers without support for scripts, so it doesn't show as plain text. |
| `$conditions` | **string\|null** | Optional conditions for generating a conditional comment (default: null). |

**Return Value:**

`string` - Return he generated HTML comment string.

***

### css

Generate an inline style element with the given CSS content.

This method escapes the provided CSS content to prevent XSS and returns the
generated HTML for the style element.

```php
public static css(string $content, array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The CSS content to be included within the style tag. |
| `$attributes` | **array<string,string>** | Additional HTML attributes for the style element (e.g., class, id). |

**Return Value:**

`string` - Return the generated HTML style element as a string.

***

### js

Generate an inline script element with the given JavaScript content.

This method escapes the provided JavaScript content to prevent XSS and returns the
generated HTML for the script element.

```php
public static js(string $content, array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The JavaScript content to be included within the script tag. |
| `$attributes` | **array<string,string>** | Additional HTML attributes for the script element (e.g., async, defer). |

**Return Value:**

`string` - Return the generated HTML script element as a string.

***

### hidden

Generate a hidden div element with the given content.

This method sets the display style to 'none' to hide the element from view.

```php
public static hidden(string $content, array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The content to be included within the div. |
| `$attributes` | **array<string,string>** | Additional HTML attributes for the div (e.g., class, id). |

**Return Value:**

`string` - Return the generated HTML div element as a string.

***

### invisible

Generate an invisible div element with the given content.

This method either hides the element with absolute positioning (focusable)
or sets its visibility to hidden, based on the focusable parameter.

```php
public static invisible(string $content, bool $focusable = true, array<string,string> $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The content to be included within the div. |
| `$focusable` | **bool** | Whether the element should be focusable or not. |
| `$attributes` | **array<string,string>** | Additional HTML attributes for the div (e.g., class, id). |

**Return Value:**

`string` - Return the generated HTML div element as a string.

***

### list

Generate an HTML list (unordered or ordered) from a set of items.

This method supports both an array of items and a single string.
If the input is an array, each item can include its content and attributes.

```php
public static list(
    array<int,string|array<string,string>>|string $items, 
    string $type = 'ul', 
    array<string,string> $attributes = []
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$items` | **array<int,string<array<string,string>>&#124;string** | The items to be included in the list; can be a single string or an array of items.<br />Each item can be a string or an associative array with 'content' and 'attributes'. |
| `$type` | **string** | The type of list to generate ('ul' for unordered, 'ol' for ordered). Default is 'ul'. |
| `$attributes` | **array<string,string>** | Additional HTML attributes for the list element (e.g., class, id). |

**Return Value:**

`string` - Return the generated HTML list as a string.

***

### ol

Generates an ordered list (ol) with items.

```php
public static ol(string $list, array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$list` | **string** | The HTML list items. |
| `$attributes` | **array** | Optional HTML attributes for the list. |

**Return Value:**

`string` - Return the generated ordered list.

**See Also:**

* https://www.w3schools.com/TAGS/tag_ol.asp

***

### ul

Generates an unordered list (ul) with items.

```php
public static ul(string $list, array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$list` | **string** | The HTML list items. |
| `$attributes` | **array** | Optional HTML attributes for the list. |

**Return Value:**

`string` - Return the generated unordered list.

**See Also:**

* https://www.w3schools.com/TAGS/tag_ul.asp

***

### link

Generates a link element (a).

```php
public static link(string $url, string|null $text = null, array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$url` | **string** | The URL for the link. |
| `$text` | **string&#124;null** | The visible text for the link or null to display url as text. |
| `$attributes` | **array** | Optional HTML attributes for the link. |

**Return Value:**

`string` - Return the generated link.

***

### image

Generates an image element (img).

```php
public static image(string $src, array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$src` | **string** | The source URL for the image. |
| `$attributes` | **array** | Optional HTML attributes for the image. |

**Return Value:**

`string` - Return the generated image element.

***

### picture

Generates a picture element with a fallback img.

```php
public static picture(
	string $src, 
	array|string $source, 
	array<string,array> $attributes = ['image' => [], 'picture' => []]
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$src` | **string** | The source URL for the image. |
| `$source` | **array&#124;string** | The source(s) for the picture. |
| `$attributes` | **array** | Optional attributes for the `img` and `picture` tags.<br />- 'image'  =&gt; (array) Attributes for the fallback `&lt;img&gt;` tag (e.g., 'alt', 'style').<br />- 'picture' =&gt; (array) Attributes for the `&lt;picture&gt;` tag. |

**Return Value:**

`string` - Return the generated picture element.

***

### figure

Generates a figure element containing an image with an optional caption.

```php
public static figure(
	string $src, 
	string $caption, 
	array<string,array> $attributes = ['image' => [], 'figure' => []]
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$src` | **string** | The URL of the image. |
| `$caption` | **string** | The caption text for the image. |
| `$attributes` | **array** | Optional attributes for the `img` and `figure` tags.<br />- 'image'  =&gt; (array) Attributes for the `&lt;img&gt;` tag (e.g., 'alt', 'style').<br />- 'figure' =&gt; (array) Attributes for the `&lt;figure&gt;` tag. |

**Return Value:**

`string` - Return the generated figure element.

***

### source

Generates an HTML &lt;source&gt; element for media content, such as &lt;video&gt; or &lt;audio&gt; tags.

```php
public static source(string $src, string $type = 'audio/mpeg', array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$src` | **string** | The URL of the media file. |
| `$type` | **string** | The MIME type of the media file (e.g., 'video/mp4', 'audio/mpeg'). |
| `$attributes` | **array** | Optional attributes for the source element. |

**Return Value:**

`string` - Return the generated HTML `&lt;source&gt;` element as a string.

**See Also:**

* https://www.w3schools.com/TAGS/tag_source.asp

***

### track

Generates an HTML &lt;track&gt; element for media content, such as &lt;video&gt; or &lt;audio&gt; tags.

```php
public static track(string $src, string $kind = 'subtitles', array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$src` | **string** | The URL of the media file. |
| `$kind` | **string** | Specifies the kind of text track (e.g., 'captions', 'chapters', `descriptions`, `metadata` or `subtitles`). |
| `$attributes` | **array** | Optional attributes for the source element. |

**Return Value:**

`string` - Return the generated HTML `&lt;track&gt;` element as a string.

**See Also:**

* https://www.w3schools.com/TAGS/tag_track.asp

***

### param

Generates an HTML &lt;param&gt; element for media content, such as &lt;object&gt; tags.

```php
public static param(string $name, string $value): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of a parameter (e.g, `autoplay`). |
| `$value` | **string** |  |

**Return Value:**

`string` - Return the generated HTML `&lt;param&gt;` element as a string.

**See Also:**

* https://www.w3schools.com/TAGS/tag_param.asp

***

### video

Generates a video element.

```php
public static video(
    array|string $source, 
    array|string $tracks = [], 
    array $attributes = [], 
    string $placeholder = 'Your browser does not support the video tag.'
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$source` | **array&#124;string** | The source(s) for the video. |
| `$tracks` | **array&#124;string** | The track(s) for the video. |
| `$attributes` | **array** | Optional attributes for the video element. |
| `$placeholder` | **string** | Fallback text if the video is unsupported. |

**Return Value:**

`string` - Return the generated video element.

**See Also:**

* https://www.w3schools.com/TAGS/tag_video.asp 

***

### audio

Generates an audio element.

```php
public static audio(
    array|string $source, 
    array|string $tracks = [], 
    array $attributes = [], 
    string $placeholder = 'Your browser does not support the audio tag.'
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$source` | **array&#124;string** | The source(s) for the audio. |
| `$tracks` | **array&#124;string** | The track(s) for the audio. |
| `$attributes` | **array** | Optional attributes for the audio element. |
| `$placeholder` | **string** | Fallback text if the audio is unsupported. |

**Return Value:**

`string` - Return the generated audio element.

**See Also:**

* https://www.w3schools.com/TAGS/tag_audio.asp 

***

### iframe

Generates an iframe element.

```php
public static iframe(
    string $src, 
    string $placeholder = 'Your browser does not support the iframe tag.', 
    array $attributes = []
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$src` | **string** | The source URL for the iframe content. |
| `$placeholder` | **string** | The fallback text or content inside the iframe element. |
| `$attributes` | **array** | Optional HTML attributes for the iframe. |

**Return Value:**

`string` - Return the generated iframe element.

***

### map

Generates an HTML image map with the specified areas and attributes.

This creates an &lt;img&gt; element with a usemap attribute, along with the associated &lt;map&gt; and &lt;area&gt; tags.

```php
public static map(
	string $src, 
	string $name, 
	array $areas, 
	array<string,array> $attributes = ['image' => [], 'map' => []]
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$src` | **string** | The source URL of the image to be used in the map. |
| `$name` | **string** | The name of the image map (used in the &quot;usemap&quot; attribute). |
| `$areas` | **string&#124;array** | An array or string defining the areas within the map. |
| `$attributes` | **array<string,array>** | Optional attributes for the `img` and `map` elements.<br />- 'image' =&gt; array for &lt;img&gt; element attributes<br />- 'map' =&gt; array for &lt;map&gt; element attributes |

**Return Value:**

`string` - Return the complete HTML string for the image and the associated map element.

***

### document

Generates a full basic HTML document structure.

For `$headers` argument, each element in the array should be an associative array containing the following keys:

- **tag** (string): The HTML tag to generate (e.g., 'meta', 'link', 'script').
- **content** (string): Optional content inside the tag (e.g., for `script`).
- **closeElement** (bool): Whether to close the tag as a block element (default: true if content is provided).
- **attributes** (array): Optional associative array of HTML attributes for the tag.

```php
public static document(
    string $content, 
    string $title, 
    string $doctype = 'html5', 
    string|array|null $headers = null, 
    array<string,array> $attributes = ['html' => [], 'body' => []]
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The HTML content of the page. |
| `$title` | **string** | The title of the page. |
| `$doctype` | **string** | The HTML document type declaration (default: 'html5'). |
| `$headers` | **string&#124;array&#124;null** | Optional elements for the document's &lt;head&gt; (e.g., &lt;meta&gt;, &lt;link&gt;, &lt;script&gt;). |
| `$attributes` | **array** | Optional attributes for the &lt;html&gt; and &lt;body&gt; tags.<br />- 'html' =&gt; attributes for the &lt;html&gt; tag.<br />- 'body' =&gt; attributes for the &lt;body&gt; tag. |

**Return Value:**

`string` - Return the generated HTML document.

***

### xml

Generates a full basic XML document structure with a flexible doctype and additional options.

```php
public static xml(
    string $content, 
    string $version = '1.0', 
    string $encoding = 'UTF-8', 
    bool $standalone = true, 
    string $doctype = 'xhtml11', 
    array $attributes = []
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The XML content of the document. |
| `$version` | **string** | The XML version (default: '1.0'). |
| `$encoding` | **string** | The character encoding of the document (default: 'UTF-8'). |
| `$standalone` | **bool** | Whether the XML document is standalone (default: true). |
| `$doctype` | **string** | The XML document type declaration (default: 'xhtml11'). |
| `$attributes` | **array** | Optional attributes for the root element. |

**Return Value:**

`string` - Return the generated XML document.

***

### svg

Generates a full basic XML document structure.

```php
public static svg(string $content, string $doctype = 'svg10', array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$content` | **string** | The SVG content of the document. |
| `$doctype` | **string** | The SVG document type (default:`svg10`). |
| `$attributes` | **array** | Optional attributes for the `svg` tag. |

**Return Value:**

`string` - Return the generated HTML document.

***

### table

Generates an HTML table with headers, body, and footers.

```php
public static table(
    string|array $tbody, 
    string|array|null $thead = null, 
    string|array|null $tfoot = null, 
    string|array|null $colgroup = null, 
    array $attributes = [], 
    string|null $caption = null
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tbody` | **string&#124;array** | The table body (string or array). |
| `$thead` | **string&#124;array&#124;null** | The table headers (string or array). |
| `$tfoot` | **string&#124;array&#124;null** | The table footers (string or array). |
| `$colgroup` | **string&#124;array&#124;null** | Attributes for table column group (string or array). |
| `$attributes` | **array** | Optional HTML attributes for the table. |
| `$caption` | **string&#124;null** | Optional caption for the table (default: null). |

**Return Value:**

`string` - Return the generated HTML table.

***

### tcol

Generates a table column group <colgroup> with type.

```php
public static tcol(array<int,array<string,mixed>> $columns, array $attribute = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,array<string,mixed>>** | An array of table columns attributes for each row. |
| `$attribute` | **array** |  |

**Return Value:**

`string` - Return the generated colgroup and columns element.

***

### tcell

Generates a table cell with type.

```php
public static tcell(string $parent, string $type, array $rows, array $attribute = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$parent` | **string** | Table cell parent element type (e.g, `thead`, `tbody`, `tfoot`). |
| `$type` | **string** | Table cell children type (e.g, `td`, `th`). |
| `$rows` | **array** | The rows of the table body. Each row can be:<br />- An array of strings (e.g., ['foo', 'bar'])<br />- An array of associative arrays (e.g., [['content' =&gt; 'Foo', 'attributes' =&gt; [...]], ...]) |
| `$attribute` | **array** |  |

**Return Value:**

`string` - Return the generated table cell element.

***

### tbody

Generates a table body (tbody) with rows.

```php
public static tbody(array $rows, string $type = 'td', array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$rows` | **array** | The rows of the table body. Each row can be:<br />- An array of strings (e.g., ['foo', 'bar'])<br />- An array of associative arrays (e.g., [['value' =&gt; 'Foo', 'attributes' =&gt; [...]], ...]). |
| `$type` | **string** | The child element tag name of the table tbody (default: `td`). |
| `$attributes` | **array** | Optional HTML attributes for the tbody. |

**Return Value:**

`string` - Return the generated tbody element.

***

### thead

Generates a table head (thead) with rows.

```php
public static thead(array $rows, string $type = 'th', array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$rows` | **array** | The rows of the table head. |
| `$type` | **string** | The child element tag name of the table thead (default: `th`). |
| `$attributes` | **array** | Optional HTML attributes for the thead. |

**Return Value:**

`string` - Return the generated thead element.

***

### tfoot

Generates a table footer (tfoot) with rows.

```php
public static tfoot(array $rows, string $type = 'td', array $attributes = []): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$rows` | **array** | The rows of the table footer. |
| `$type` | **string** | The child element tag name of the table tfoot (default: `td`). |
| `$attributes` | **array** | Optional HTML attributes for the tfoot. |

**Return Value:**

`string` - Return the generated tfoot element.