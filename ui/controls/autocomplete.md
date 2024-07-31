# Autocomplete

### Autocomplete

Autocomplete control.

```html
<input
    class="form-control"
    placeholder="Search for something or else"
    data-control="autocomplete"
    data-source="something: 'Something', else: 'Else'"
/>
```

```backend
<input
    class="form-control"
    placeholder="Search for something or else"
    data-control="autocomplete"
    data-source="something: 'Something', else: 'Else'"
/>
```

## JavaScript API

```js
$('input').autocomplete({
    source: { something: 'Something', else: 'Else' }
})
```
