# | media

The `| media` filter returns an address relative to the public path of the [media manager library](../cms/mediamanager). The result is a URL to the media file specified in the filter parameter.

    <img src="{{ 'banner.jpg' | media }}" />

If the media manager address is `https://cdn.wintercms.com` the above example would output the following:

    <img src="https://cdn.wintercms.com/banner.jpg" />
