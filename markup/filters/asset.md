# | asset

The asset filter (also available as a function, `asset()`) generates a URL for an asset using the current scheme of the request (HTTP or HTTPS):

```twig
{{ 'img/photo.jpg' | asset }}

{# or #}

{{ asset('img/photo.jpg') }}
```

See [Helpers#asset](../services/helpers#method-asset) for more information.
