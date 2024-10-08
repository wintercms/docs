# Tooltips

Tooltips are an alternative to the standard browser title tooltip.

## Tooltip markup
A standard tooltip

```html
<div class="tooltip fade top in">
    <div class="tooltip-arrow"></div>
    <div class="tooltip-inner">Create a new blog post based on this</div>
</div>
```

```backend
<div class="tooltip fade top in">
    <div class="tooltip-arrow"></div>
    <div class="tooltip-inner">Create a new blog post based on this</div>
</div>
```

## Spawning tooltips
Tooltips can be automatically created when the mouse enters an element using the `data-toggle="tooltip"` tag.

```html
<a
    href="javascript:;"
    data-toggle="tooltip"
    data-placement="left"
    data-delay="500"
    title="Tooltip content">
    Some link
</a>
```

```backend
<a
    href="javascript:;"
    data-toggle="tooltip"
    data-placement="left"
    data-delay="500"
    title="Tooltip content">
    Some link
</a>
```

## Example

```html
<div class="tooltip fade top in">
    <div class="tooltip-arrow"></div>
    <div class="tooltip-inner">Create a new blog post based on this</div>
</div>
```

```backend
<div class="tooltip fade top in">
    <div class="tooltip-arrow"></div>
    <div class="tooltip-inner">Create a new blog post based on this</div>
</div>
```
