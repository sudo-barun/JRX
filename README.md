# JRX (JSON Representation of XML)

## Introduction

JRX (JSON Representation of XML) is the usage of JSON to represent XML-based content like HTML and SVG.

JRX only represents element node, text node and comment node. It cannot represent other valid contents like XML declaration, Document Type Declaration declaration (in HTML), CDATA and so on.

## Why use JRX

1. JRX can be used as template to generate content based on XML or XML based languages like HTML and SVG.
1. Since JRX resembles XML, it makes easy to visualize XML structure that it represents.
1. JRX removes the concern for encoding/decoding of text content.

## Uses

1. Client-side Javascript code can use JRX for HTML template.
	<br>
	This eliminates the build process to convert HTML template to intermediate format.
1. WYSIWYG editor can use JRX to store the content.


## Rules

0. Must be valid JSON.

1. Must start with array `[ ]`.

1. Element node starts with element start marker: `"<element-name>"` and ends with element end marker `"</element-name>"`.
	<br>
	Example:
	<br>
	`<div>` in HTML can be represented as:
	```JSON
	[ "<div>", "</div>" ]
	```
	`<g>` in SVG can be represented as:
	```JSON
	[ "<g>", "</g>" ]
	```

1. Element node can also start with self-closing element start marker: `"<element-name/>"`. This is for empty/void element.
	<br>
	Example:
	<br>
	`<img>` in HTML can be represented as:
	```JSON
	[ "<img/>" ]
	```
	`<circle>` in SVG can be represented as:
	```JSON
	[ "<circle/>" ]
	```

1. Element start and end markers are case sensitive. Element starting with `"<element-name>"` must end with `"</element-name>"` and element starting with `"<ELEMENT-NAME>"` must end with `"</ELEMENT-NAME>"`.

1. Attributes of element node are contained within object literal right after the element start marker.
	<br>
	Example:
	```JSON
	[
		"<div>", {
			"class": "container",
			"style": "color: red; font-weight: bold"
		},
		"</div>"
	]
	```
	```JSON
	[ "<img/>", { "src": "image.jpg" } ]
	```

1. Attribute name and value can only be string.

1. Element node without any attribute may omit empty object although having empty literal is valid as well.

1. Child nodes of element node are contained within array `[ ]`.
	<br>
	Example:
	<br>
	```XML
	<div>
		<div></div>
		<div></div>
	</div>
	```
	The above HTML is represented as:
	```JSON
	[
		"<div>",
		[
			"<div>", "</div>",
			"<div>", "<div>"
		],
		"</div>"
	]
	```

1. Element node without any child node may omit empty array although having empty array is valid as well.

1. Text node starts with text start marker `"<>"`, ends with text end marker `"</>"` and has text content in-between.
	Example:
	```JSON
	[
		"<>", "This is a text.", "</>",
		"<p>",
		[
			"<>", "This is a paragraph.", "</>"
		]
		"</p>"
	]
	```

1. There should be exactly one string inside text start marker and text end marker.

1. Text node cannot have text node as sibling. Example, the code below is invalid JRX:
	```JSON
	[
		"<>", "Hello", "</>",
		"<>", " World", "</>",
		"<div>", "</div>"
	]
	```
	The correct code would be:
	```JSON
	[
		"<>", "Hello World", "</>",
		"<div>", "</div>"
	]
	```
	OR
	```JSON
	[
		"<>", "Hello", "</>",
		"<!--", "", "-->",
		"<>", " World", "</>",
		"<div>", "</div>"
	]
	```

	The reason this is not allowed

1. Text node does not have to be escaped. Special characters like `<`, `&`, `"` should be written in original (non-encoded) form.
	<br>
	Example:
	```XML
	<span>Cat &amp; Dog</span>
	```
	The above HTML is represented as:
	```JSON
	[
		"<span>", [ "<>", "Cat & Dog" "</>" ], "</span>"
	]
	```

1. Comment node starts with comment start marker `<!--`, ends with comment end marker `-->` and has text content in-between.
	<br>
	Example:
	```JSON
	[
		"<!--", "Copyright © Barun Kharel", "-->"
	]
	```

1. There should be exactly one string inside comment start marker and comment end marker.

1. Similar to text node, text content of comment node does not have to be escaped.

1. Every nodes must be closed. Some examples of invalid JRX due to unclosed node:
	```JSON
	[ "<div>", [ "<img>" ], "</div>" ]
	```
	```JSON
	[ "<>", "content of text node" ]
	```
	```JSON
	[ "<!--", "content of comment node" ]
	```


## Examples

### 1. HTML:

```XML
<div class="container"><h1>Main Heading</h1><p>This is text content of a paragraph. This is a <b>bold</b> text.</p></div>
```

The above HTML is represented as following using JRX:

JRX:
```JSON
[
	"<div>", {
		"class": "container"
	},
	[
		"<h1>",
		[
			"<>", "Main Heading", "</>"	
		],
		"</h1>",
		"<p>",
		[
			"<>", "This is text content of a paragraph. There is a ", "</>"
			"<b>", "bold", "</b>",
			"<>", " text.", "</>"
		],
		"</p>"
	],
	"</div>"
]
```

### 2. SVG:

```XML
<svg width="100" height="100"><circle cx="50" cy="50" r="40" stroke="green" stroke-width="4" fill="yellow"/></svg>
```

The above SVG is represented as following using JRX:

JRX:
```JSON
[
	"<svg>", {
		"width": "100",
		"height": "100"
	},
	[
		"<circle/>", {
			"cx": "50",
			"cy": "50",
			"r": "40",
			"stroke": "green",
			"stroke-width": "4",
			"fill": "yellow"
		}
	],
	"</svg>"
]
```
