---
title: "Filters: md"
description: "Documentation on the 'md' Twig filter family."
---
# md

## Standard usage

The `| md` filter converts the value from Markdown to HTML format.

```twig
{{ '**Text** is bold.' | md }}
```

The above will output the following:

```html
<p><strong>Text</strong> is bold.</p>
```

## Converting a single line

The `| md_line` filter converts a single line from Markdown to HTML format, as an inline element.

```twig
{{ '**Text** is bold.' | md_line }}
```

The above will output the following:

```html
<strong>Text</strong> is bold.
```

## Safe conversion

The `| md_safe` filter converts the value from Markdown to HTML format, preventing `<code>` blocks caused by indentation.

```twig
{{ '    **Text** is bold.' | md_safe }}
```

The above will output the following:

```html
<p><strong>Text</strong> is bold.</p>
```

instead of:

```html
<pre><code><strong>Text</strong> is bold.</p></code></pre>
```
