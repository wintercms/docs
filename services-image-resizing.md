# Image Resizing

- [Introduction](#introduction)
- [Configuration](#configuration)
- [Usage](#usage)
- [Available Parameters](#resize-parameters)
- [Available Modes](#available-modes)
- [Available Sources](#resize-sources)

<a name="introduction"></a>
## Introduction

The Image Resizing service can be used for resizing any image resources accessible to the application.

It works by accepting a variety of image sources and normalizing the pipeline for storing the desired resizing configuration and then deferring the actual resizing of the images until requested by the browser. When the resizer route is hit, the configuration is retrieved from the cache and used to generate the desired image and then redirect to the generated images static path to minimize the load on the server.

Future loads of the image are automatically pointed to the static URL of the resized image without even hitting the resizer route.

<a name="configuration"></a>
## Configuration

The functionality of this class is controlled by these config items:

- **cms.storage.resized.disk**: The disk to store resized images on
- **cms.storage.resized.folder**: The folder on the disk to store resized images in
- **cms.storage.resized.path**: The public path to the resized images as returned by the storage disk's URL method, used to identify already resized images

> **NOTE:** The image resizing service requires a cache driver that persists cache data between requests in order to function, `array` is not a supported cache driver if you wish to use this service.

<a name="usage"></a>
## Usage

The Image Resizer can be access through a number of different methods:

- [`image` backend list columns](../backend/lists#column-image)
- [`getThumb()` on `Winter\Storm\Database\Attach\File` models](../database/attachments#viewing-attachments)
- the [`| resize` filter](../markup/filter-resize)
- Passing a supported `$image` source to [`System\Classes\ImageResizer::filterGetUrl()`](https://wintercms.com/docs/api/develop/System/Classes/ImageResizer.html#method_filterGetUrl)
- Instantiating an instance of the [`System\Classes\ImageResizer` class](https://wintercms.com/docs/api/develop/System/Classes/ImageResizer.html#method___construct) and using that as desired

<a name="resize-parameters"></a>
## Available Parameters

The basic parameters provided to the ImageResizer are `(int) $width`, `(int) $height`, and `(array) $options`.

If `$width` or `$height` is falsey or `'auto'`, that value is calculated using original image ratio (automatic proportional scaling).

The following elements are supported in the options array are supported:

Key | Description | Default | Options
--- | --- | --- | ---
`mode` | How the image should be fitted to dimensions | `auto` | `exact`, `portrait`, `landscape`, `auto`, `fit`, or `crop`
`offset` | Offset the crop of the resized image | `[0,0]` | [left, top]
`quality` | Quality of the resized image | `90` | `0-100`
`sharpen` | Amount to sharpen the image | `0` | `0-100`

<a name="available-modes"></a>
## Available Modes

The `mode` option allows you to specify how the image should be resized. The available modes are as follows:

Mode | Description
--- | ---
`auto` | Automatically choose between `portrait` and `landscape` based on the image's orientation
`exact` | Resize to the exact dimensions given, without preserving aspect ratio
`portrait` | Resize to the given height and adapt the width to preserve aspect ratio
`landscape` | Resize to the given width and adapt the height to preserve aspect ratio
`crop` | Crop to the given dimensions after fitting as much of the image as possible inside those
`fit` | Fit the image inside the given maximal dimensions, keeping the aspect ratio

<a name="resize-sources"></a>
## Available Sources

The available sources that images can be resized from are as follows:

- Plugins
- Themes
- Media Library
- Uploads Directory
- Models that extend the `\Winter\Storm\Database\Attach\File` model

Available sources can be extended by listening to the [`system.resizer.getAvailableSources` event](../events/event/system.resizer.getAvailableSources)
