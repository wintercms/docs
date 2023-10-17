---
title: "Filters: default"
description: "Documentation on the 'default' Twig filter."
---
# default

## Usage

The `| default` filter returns the value passed as the argument if the filtered value is undefined or empty, otherwise the filtered value is returned.

```twig
{{ variable | default('The variable is not defined') }}

{# The above tag would be displayed as "The variable is not defined" #}

{% set variable = 'Variable is now defined' %}
{{ variable | default('The variable is not defined') }}

{# The above tag would now be displayed as "Variable is now defined" #}
```

You may also use this filter for method calls:

```twig
{{ object.getName(variable | default('Unknown')) }}
```
