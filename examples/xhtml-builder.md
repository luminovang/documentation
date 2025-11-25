# Document Builder Examples

***

## Overview

This documentation offers examples of how to use the Xhtml class to generate various HTML elements.

***

## Introduction

These examples show the versatility of the [Xhtml Markup Builder Class](/documents/xhtml.md) in generating HTML elements dynamically within Luminova application. The class provides several utility methods to ease the process of creating HTML structures, allowing you to easily define elements like lists, images, links, videos, and even entire HTML documents.

***

### Basic Document

```php
use Luminova\Component\Widget\Xhtml;

$htmlContent = Xhtml::document(
    '<h1>Welcome to Luminova</h1><p>This is a basic HTML document example.</p>',
    'Luminova Example'
);

echo $htmlContent;
```

**Output:**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Luminova Example</title>
</head>
<body>
    <h1>Welcome to Luminova</h1>
    <p>This is a basic HTML document example.</p>
</body>
</html>
```

***

### Advance Document

```php
use Luminova\Component\Widget\Xhtml;

$headers = [
    [
        'tag' => 'meta',
        'attributes' => ['charset' => 'UTF-8'],
    ],
    [
        'tag' => 'link',
        'attributes' => [
            'rel' => 'stylesheet',
            'href' => 'style.css'
        ]
    ],
];

$attributes = [
    'html' => ['lang' => 'en'],
    'body' => ['class' => 'homepage', 'id' => 'main-body']
];

echo Xhtml::document(
    '<h1>Welcome to Luminova</h1>', 
    'Home', 
    'html5'
    $headers, 
    $attributes
);
```

**Output:**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Home</title>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="style.css">
</head>
<body class="homepage" id="main-body">
    <h1>Welcome to Luminova</h1>
</body>
</html>

```

***

### Doctype Declaration

```php
echo Xhtml::doctype('html5'); // Generates HTML5 doctype
```

**Output:**
```html
<!DOCTYPE html>
```

***

### Dynamic Elements

You can generate any HTML element using the `__callStatic` method (e.g, `Xhtml::TagName(...)`).

**Generating a `<div>` Element**

The `div()` method generates a `<div>` element with optional content, attributes, and the option to include a closing tag.

```php
use Luminova\Component\Widget\Xhtml;

$divAttributes = ['class' => 'container', 'id' => 'main-container'];
$divContent = 'This is a container div.';

echo Xhtml::div($divContent, true, $divAttributes);
```

**Output:**

```html
<div class="container" id="main-container">
    This is a container div.
</div>
```

**Example without Closing the Tag:**

```php
$divAttributes = ['class' => 'container'];
echo Xhtml::div('', false, $divAttributes); // Will not close the tag
```

**Output:**
```html
<div class="container">
```

***

**Generating a `<p>` Element**

The `p()` method generates a `<p>` element, which can include content, optional attributes, and control over whether to close the tag.

```php
$pAttributes = ['class' => 'intro-text'];
$pContent = 'This is an introductory paragraph.';

echo Xhtml::p($pContent, true, $pAttributes);
```

**Output:**
```html
<p class="intro-text">
    This is an introductory paragraph.
</p>
```

***

**Generating a `<span>` Element**

The `span()` method generates a `<span>` element, which is used for inline content. You can include optional attributes and decide whether to close the tag.

```php
$spanAttributes = ['style' => 'color: red;'];
$spanContent = 'Important text in red.';

echo Xhtml::span($spanContent, true, $spanAttributes);
```

**Output:**
```html
<span style="color: red;">
    Important text in red.
</span>
```

***

### HTML Comments

```php
echo Xhtml::comment('This is a comment');
```

**Output:**

```html
<!--This is a comment-->
```

***

### JS Comments

Add script with comments to hide from non-supporting browsers.

```php
$content = <<<JS
function showMsg() {
  alert("Hello World!")
}
JS>>>

$comment = Xhtml::comment($content, true);
echo Xhtml::script($comment, true, [
    'type' => 'text/javascript'
]);
```

**Output:**

```html
<script type="text/javascript">
<!--
function showMsg() {
  alert("Hello World!")
}
//-->
</script>
```
***

### Conditional Comment 

This script generates conditional comments specifically for Internet Explorer versions 8 and below.

```php
echo Xhtml::comment('<script src="8-up.js"></script>', false, 'lt IE 9');
```

**Output:**
```html
<!--[if lt IE 9]>
    <script src="8-up.js"></script>
<![endif]-->
```

***

**Using Logical Operators**:

This example targets both IE 8 and IE 9.

```php
echo Xhtml::comment('<script src="8-9.js"></script>', false, 'lt IE 10 || IE 9');
```

**Output:**
```html
<!--[if lt IE 10]>
    <script src="8-9.js"></script>
<![endif]-->
```

***

### Inline Styles

```php
echo Xhtml::css('body { background-color: #fff; }');
```

**Or using style method:**

```php
echo Xhtml::style('body { background-color: #fff; }');
```

**Output:**

```html
<style>body { background-color: #fff; }</style>
```

***

### Inline JavaScript Element

```php
echo Xhtml::js('console.log("Hello World!");');
```

```php
echo Xhtml::script('console.log("Hello World!");');
```

**Output:**
```html
<script>console.log("Hello World!");</script>
```

***

### Hidden Div Element

```php
echo Xhtml::hidden('This content is hidden');
```

**Output:**
```html
<div style="display:none;">This content is hidden</div>
```

***

### Invisible Div Element

This version makes the content invisible but still occupies space (retains width and height of the content).

```php
echo Xhtml::invisible('This content is invisible', false);
```

**Output:**
```html
<div style="visibility: hidden;">This content is invisible</div>
```

***

### Focusable Invisible Div Element

This version hides the content completely (does not occupy width or height), but remains focusable and accessible to screen readers.

```php
echo Xhtml::invisible('This content is focusable and invisible', true);
```

**Output:**
```html
<div style="position: absolute; 
    width: 1px; 
    height: 1px; 
    padding: 0; 
    margin: -1px; 
    overflow: hidden; 
    clip: rect(0,0,0,0); 
    white-space: nowrap; 
    border: 0;">
    This content is focusable and invisible
</div>
```

***

### Ordered List

```php
$list = Xhtml::list(['Item 1', 'Item 2', 'Item 3']);
echo Xhtml::ol($list);
```

**Output:**
```html
<ol>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ol>
```

***

### Unordered List

```php
$list = Xhtml::list(['Item A', 'Item B', 'Item C']);
echo Xhtml::ul($list);
```

**Output:**
```html
<ul>
    <li>Item A</li>
    <li>Item B</li>
    <li>Item C</li>
</ul>
```

***

### Hyper Links

```php
echo Xhtml::href('https://www.example.com', 'Visit Example', ['class' => 'link']);
```

**Output:**

```html
<a href="https://www.example.com" class="link">Visit Example</a>
```

**Using the tag name:**

```php
$url = 'https://www.example.com';

echo Xhtml::a($url, true, [
    'class' => 'link',
    'href' => $url
]);
```

**Output:**

```html
<a href="https://www.example.com" class="link">https://www.example.com</a>
```

***

### Link Tag

```php
echo Xhtml::link(null, false, [
	'rel' => 'shortcut icon',
	'href' => 'assets/images/favicon.png'
]);
```

**Output:**

```html
<link rel="shortcut icon" href="assets/images/favicon.png" />
```

***

### Meta Tag

```php
echo Xhtml::meta(null, false, [
	'name' => 'mobile-web-app-capable',
	'content' => 'yes'
]);
```

**Output:**

```html
<meta name="mobile-web-app-capable" content="yes" />
```

***

### Script Tag

```php
echo Xhtml::script(null, true, [
	'src' => 'assets/js/app.js',
	'async' => null
]);
```

**Output:**

```html
<script src="assets/js/app.js" async></script>
```

***

### Image Element

```php
echo Xhtml::image('https://www.example.com/image.jpg', ['alt' => 'Example Image', 'class' => 'image-class']);
```

**Output:**

```html
<img src="https://www.example.com/image.jpg" alt="Example Image" class="image-class" />
```

***

### Picture Element

```php
$sources = [
    ['src' => 'small.jpg', 'media' => '(max-width: 600px)'],
    ['src' => 'large.jpg', 'media' => '(min-width: 601px)']
];
echo Xhtml::picture('fallback.jpg', $sources);
```

**Output:**

```html
<picture>
    <source src="small.jpg" media="(max-width: 600px)" />
    <source src="large.jpg" media="(min-width: 601px)" />
    <img src="fallback.jpg" style="width:auto;" alt="Image" />
</picture>
```

***

### Iframe Element

```php
echo Xhtml::iframe('https://www.example.com', 'This is a placeholder');
```

**Output:**
```html
<iframe src="https://www.example.com">This is a placeholder</iframe>
```

***

### Image Map

```php
$areas = [
    ['shape' => 'rect', 'coords' => '34,44,270,350', 'href' => 'link1.html', 'alt' => 'Link 1'],
    ['shape' => 'circle', 'coords' => '250,150,50', 'href' => 'link2.html', 'alt' => 'Link 2']
];
echo Xhtml::map('image.jpg', 'example-map', $areas);
```

**Output:**

```html
<img src="image.jpg" usemap="#example-map" alt="example-map" id="img-map-uniqueid" />
<map name="example-map">
    <area shape="rect" coords="34,44,270,350" href="link1.html" alt="Link 1" />
    <area shape="circle" coords="250,150,50" href="link2.html" alt="Link 2" />
</map>
```

***

### Video Element

The `video()` method allows you to generate a `<video>` element with one or more video sources, optional tracks, and additional attributes. It also provides a placeholder message for browsers that do not support the video tag.

**Example with Multiple Video Sources and Tracks:**

```php
use Luminova\Component\Widget\Xhtml;

$videoSources = [
    ['src' => 'video.mp4', 'type' => 'video/mp4'],
    ['src' => 'video.webm', 'type' => 'video/webm']
];

$videoTracks = [
    ['src' => 'subtitles_en.vtt', 'kind' => 'subtitles', 'srclang' => 'en', 'label' => 'English'],
    ['src' => 'subtitles_es.vtt', 'kind' => 'subtitles', 'srclang' => 'es', 'label' => 'Spanish']
];

$videoAttributes = ['controls' => true, 'width' => '640', 'height' => '360'];

echo Xhtml::video($videoSources, $videoTracks, $videoAttributes);
```

**Output:**
```html
<video controls="true" width="640" height="360">
    <source src="video.mp4" type="video/mp4" />
    <source src="video.webm" type="video/webm" />
    <track src="subtitles_en.vtt" kind="subtitles" srclang="en" label="English" />
    <track src="subtitles_es.vtt" kind="subtitles" srclang="es" label="Spanish" />
    Your browser does not support the video tag.
</video>
```

***

**Example with a Single Source and No Tracks:**

```php
$videoSource = ['src' => 'video.mp4', 'type' => 'video/mp4'];
$videoAttributes = ['controls' => true];

echo Xhtml::video($videoSource, [], $videoAttributes);
```

**Output:**
```html
<video controls="true">
    <source src="video.mp4" type="video/mp4" />
    Your browser does not support the video tag.
</video>
```

***

### Audio Element

The `audio()` method allows you to generate an `<audio>` element with one or more audio sources, optional tracks, and additional attributes. It also provides a placeholder message for browsers that do not support the audio tag.

**Example with Multiple Audio Sources and Tracks:**

```php
use Luminova\Component\Widget\Xhtml;

$audioSources = [
    ['src' => 'audio.mp3', 'type' => 'audio/mpeg'],
    ['src' => 'audio.ogg', 'type' => 'audio/ogg']
];

$audioTracks = [
    ['src' => 'captions_en.vtt', 'kind' => 'captions', 'srclang' => 'en', 'label' => 'English'],
    ['src' => 'captions_es.vtt', 'kind' => 'captions', 'srclang' => 'es', 'label' => 'Spanish']
];

$audioAttributes = ['controls' => true];

echo Xhtml::audio($audioSources, $audioTracks, $audioAttributes);
```

**Output:**

```html
<audio controls="true">
    <source src="audio.mp3" type="audio/mpeg" />
    <source src="audio.ogg" type="audio/ogg" />
    <track src="captions_en.vtt" kind="captions" srclang="en" label="English" />
    <track src="captions_es.vtt" kind="captions" srclang="es" label="Spanish" />
    Your browser does not support the audio tag.
</audio>
```

***

**Example with a Single Source and No Tracks:**

```php
$audioSource = ['src' => 'audio.mp3', 'type' => 'audio/mpeg'];
$audioAttributes = ['controls' => true];

echo Xhtml::audio($audioSource, [], $audioAttributes);
```

**Output:**

```html
<audio controls="true">
    <source src="audio.mp3" type="audio/mpeg" />
    Your browser does not support the audio tag.
</audio>
```

***

### Table Element

To generate a table with rows and cells, you would use a custom loop to build the rows dynamically using the `Xhtml::element()` method or other helper methods.

**Simple Way of Generating a Table**

```php
use Luminova\Component\Widget\Xhtml;

$tableAttributes = ['border' => 1, 'class' => 'data-table'];
$thead = [
    'Name',
    'Age',
    'Occupation'
];

$tbody = [
    [
        'Peter', 
        33, 
        [
            'content' => 'Programmer',
            'attributes' => [
                'style' => 'color: green; font-weight: bold;'
            ]
        ]
    ],
    ['John Doe', 28, 'Engineer'],
    ['Jane Smith', 34, 'Designer'],
    ['Sam Brown', 45, 'Manager']
];

$tfoot = ['Members', '', '4 People'];
$colgroup = [
    ['span' => '1', 'style' => 'background-color:#f0b0b0; width: 30%;'],
    ['span' => '1', 'style' => 'background-color:#b9b94d;width: 20%;'],
    ['span' => '1', 'style' => 'width: 50%;']
];

echo Xhtml::table($tbody, $thead, $tfoot, $colgroup, $tableAttributes);
```

**Output:**

```html
<table border="1" class="data-table">
  <colgroup>
    <col span="1" style="background-color:#f0b0b0; width: 30%;">
    <col span="1" style="background-color:#b9b94d;width: 20%;">
    <col span="1" style="width: 50%;">
  </colgroup>
  <thead>
    <tr>
      <th>Name</th>
      <th>Age</th>
      <th>Occupation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Peter</td>
      <td>33</td>
      <td style="color: green; font-weight: bold;">Programmer</td>
    </tr>
    <tr>
      <td>John Doe</td>
      <td>28</td>
      <td>Engineer</td>
    </tr>
    <tr>
      <td>Jane Smith</td>
      <td>34</td>
      <td>Designer</td>
    </tr>
    <tr>
      <td>Sam Brown</td>
      <td>45</td>
      <td>Manager</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Members</td>
      <td></td>
      <td>4 People</td>
    </tr>
  </tfoot>
</table>
```

***

**Generating a table using custom implementation:**

```php
use Luminova\Component\Widget\Xhtml;

$tableAttributes = ['border' => 1, 'class' => 'data-table'];
$headerRow = Xhtml::element('tr', 
    Xhtml::element('th', 'Name') . 
    Xhtml::element('th', 'Age') . 
    Xhtml::element('th', 'Occupation')
);

$dataRows = [
    ['Peter', 33, 'Programmer'],
    ['John Doe', 28, 'Engineer'],
    ['Jane Smith', 34, 'Designer'],
    ['Sam Brown', 45, 'Manager']
];

$rows = '';
foreach ($dataRows as $data) {
    $rowContent = Xhtml::element('td', $data[0]) . 
        Xhtml::element('td', $data[1]) . 
        Xhtml::element('td', $data[2]);
    $rows .= Xhtml::element('tr', $rowContent);
}

echo Xhtml::element('table', $headerRow . $rows, true, $tableAttributes);
```

**Output:**
```html
<table border="1" class="data-table">
    <tr>
        <th>Name</th>
        <th>Age</th>
        <th>Occupation</th>
    </tr>
    <tr>
        <td>Peter</td>
        <td>33</td>
        <td>Programmer</td>
    </tr>
    <tr>
        <td>John Doe</td>
        <td>28</td>
        <td>Engineer</td>
    </tr>
    <tr>
        <td>Jane Smith</td>
        <td>34</td>
        <td>Designer</td>
    </tr>
    <tr>
        <td>Sam Brown</td>
        <td>45</td>
        <td>Manager</td>
    </tr>
</table>
```