---
title: "Filters: raw"
description: "Documentation on the 'raw' Twig filter."
---
# raw

## Usage

Variables and methods outputted in Twig templates in Winter are automatically escaped by default for security reasons. If you wish to display the raw data without any escaping, use the `| raw` filter.

```twig
{% set html = "<strong>Hi</strong>" %}

{{ html }}
{# Outputs as "&lt;strong%gt;Hi&lt;/strong%gt;" #}

{{ html | raw }}
{# Outputs as "<strong>Hi</strong>" #}
```

> **NOTE:** Exercise extreme caution when using this filter with user-inputted data.

Be careful when using the `raw` filter inside expressions:

```twig
{% set hello = '<strong>Hello</strong>' %}
{% set hola = '<strong>Hola</strong>' %}

{{ false ? '<strong>Hola</strong>' : hello | raw }}

{# The above will not render the same as #}
{{ false ? hola : hello | raw }}

{# But renders the same as #}
{{ (false ? hola : hello) | raw }}
```
