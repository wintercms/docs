# Loading Indicators

## Container Loading Indicator

#### Loading Indicator
A loading indicator used in a container.

```html
<div class="loading-indicator-container">
    <div class="loading-indicator">
        <span></span>
    </div>
    <p>This is some content inside the container</p>
    <p>The loading indicator must be prepended to it</p>
</div>
```

```backend
<div class="loading-indicator-container">
    <div class="loading-indicator">
        <span></span>
    </div>
    <p>This is some content inside the container</p>
    <p>The loading indicator must be prepended to it</p>
</div>
```

#### Text Loading Indicator
A loading indicator can have text by adding a `<div>` element inside.

```html
<div class="loading-indicator-container">
    <div class="loading-indicator">
        <span></span>
        <div>Loading...</div>
    </div>
</div>
```

```backend
<div class="loading-indicator-container">
    <div class="loading-indicator">
        <span></span>
        <div>Loading...</div>
    </div>
</div>
```

#### Loading Indicator Sizes

A loading indicator can have a size by adding `size-X` to the container. These sizes are available: **size-small**.

```html
<div class="loading-indicator-container">
    <div class="loading-indicator size-small">
        <span></span>
        <div>Loading (size-small)</div>
    </div>
</div>
```

```backend
<div class="loading-indicator-container">
    <div class="loading-indicator size-small">
        <span></span>
        <div>Loading (size-small)</div>
    </div>
</div>
```

#### Loading Indicator Alignment

A loading indicator can be aligned to the center by adding `indicator-center` to the container and/or indicator.

```html
<div class="loading-indicator-container">
    <div class="loading-indicator indicator-center">
        <span></span>
    </div>
</div>
```

```backend
<div class="loading-indicator-container">
    <div class="loading-indicator indicator-center">
        <span></span>
    </div>
</div>
```

You may add some optional text:

```html
<div class="loading-indicator-container">
    <div class="loading-indicator indicator-center">
        <span></span>
        <div>Loading...</div>
    </div>
</div>
```

```backend
<div class="loading-indicator-container">
    <div class="loading-indicator indicator-center">
        <span></span>
        <div>Loading...</div>
    </div>
</div>
```

## Example

```html
<div class="loading-indicator-container">
    <div class="loading-indicator">
        <span></span>
    </div>
    <p>This is some content inside the container</p>
    <p>The loading indicator must be prepended to it</p>
</div>

<div class="loading-indicator-container">
    <div class="loading-indicator">
        <span></span>
        <div>Loading...</div>
    </div>
</div>

<div class="loading-indicator-container">
    <div class="loading-indicator indicator-inset">
        <span></span>
        <div>Loading (inset)</div>
    </div>
</div>

<div class="loading-indicator-container">
    <div class="loading-indicator size-small">
        <span></span>
        <div>Loading (size-small)</div>
    </div>
</div>

<div class="loading-indicator-container">
    <div class="loading-indicator indicator-center">
        <span></span>
    </div>
</div>
```

```backend
<div class="loading-indicator-container">
    <div class="loading-indicator">
        <span></span>
    </div>
    <p>This is some content inside the container</p>
    <p>The loading indicator must be prepended to it</p>
</div>

<div class="loading-indicator-container">
    <div class="loading-indicator">
        <span></span>
        <div>Loading...</div>
    </div>
</div>

<div class="loading-indicator-container">
    <div class="loading-indicator indicator-inset">
        <span></span>
        <div>Loading (inset)</div>
    </div>
</div>

<div class="loading-indicator-container">
    <div class="loading-indicator size-small">
        <span></span>
        <div>Loading (size-small)</div>
    </div>
</div>

<div class="loading-indicator-container">
    <div class="loading-indicator indicator-center">
        <span></span>
    </div>
</div>
```
