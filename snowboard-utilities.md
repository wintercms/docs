# Snowboard Utilities

- [Introduction](#introduction)
- [Cookie](#cookie)
    - [Basic Usage](#cookie-basic-usage)
    - [Encoding](#cookie-encoding)
    - [Cookie Attributes](#cookie-attributes)
        - [expires](#cookie-attributes-expires)
        - [path](#cookie-attributes-path)
        - [domain](#cookie-attributes-domain)
        - [secure](#cookie-attributes-secure)
        - [sameSite](#cookie-attributes-sameSite)
        - [Setting Defaults](#cookie-attributes-defaults)
    - [Cookie Events](#cookie-events)
        - [`cookie.get`](#cookie-event-get)
        - [`cookie.set`](#cookie-event-set)
- [JSON Parser](#json-parser)
- [Sanitizer](#sanitizer)

<a name="introduction"></a>
## Introduction

The Snowboard framework included several small utilities by default that help make development easier.

<a name="cookie"></a>
## Cookie

The Cookie utility is a small wrapper around the [js-cookie](https://github.com/js-cookie/js-cookie/) package that provides a simple, lightweight JS API for interacting with browser cookies.

<a name="cookie-basic-usage"></a>
### Basic Usage

Create a cookie, valid across the entire site:

```js
Snowboard.cookie().set('name', 'value')
```

Create a cookie that expires 7 days from now, valid across the entire site:

```js
Snowboard.cookie().set('name', 'value', { expires: 7 })
```

Create an expiring cookie, valid to the path of the current page:

```js
Snowboard.cookie().set('name', 'value', { expires: 7, path: '' })
```

Read cookie:

```js
Snowboard.cookie().get('name') // => 'value'
Snowboard.cookie().get('nothing') // => undefined
```

Read all visible cookies:

```js
Snowboard.cookie().get() // => { name: 'value' }
```

>**NOTE:** Cookies can only be read if the place they are being read from has access to read the cookie according to the browser.

Delete cookie:

```js
Snowboard.cookie().remove('name')
```

Delete a cookie valid to the path of the current page:

```js
Snowboard.cookie().set('name', 'value', { path: '' })
Snowboard.cookie().remove('name') // fail!
Snowboard.cookie().remove('name', { path: '' }) // removed!
```

>**IMPORTANT!** When deleting a cookie and you're not relying on the [default attributes](#cookie-attributes-defaults), you must pass the exact same path and domain attributes that were used to set the cookie

```js
Snowboard.cookie().remove('name', { path: '', domain: '.yourdomain.com' })
```

>**NOTE:** Removing a nonexistent cookie neither raises any exception nor returns any value.

<a name="cookie-encoding"></a>
### Encoding

The package is [RFC 6265](http://tools.ietf.org/html/rfc6265#section-4.1.1) compliant. All special characters that are not allowed in the cookie-name or cookie-value are encoded with each one's UTF-8 Hex equivalent using [percent-encoding](http://en.wikipedia.org/wiki/Percent-encoding).
The only character in cookie-name or cookie-value that is allowed and still encoded is the percent `%` character, it is escaped in order to interpret percent input as literal.
Please note that the default encoding/decoding strategy is meant to be interoperable [only between cookies that are read/written by js-cookie](https://github.com/js-cookie/js-cookie/pull/200#discussion_r63270778). To override the default encoding/decoding strategy you need to use a [converter](#cookie-converters).

>**NOTE:** According to [RFC 6265](https://tools.ietf.org/html/rfc6265#section-6.1), your cookies may get deleted if they are too big or there are too many cookies in the same domain, [more details here](https://github.com/js-cookie/js-cookie/wiki/Frequently-Asked-Questions#why-are-my-cookies-being-deleted).

<a name="cookie-attributes"></a>
### Cookie Attributes

Cookie attributes can be set globally by creating an instance of the API via `withAttributes()`, or individually for each call to `Snowboard.cookie().set(...)` by passing a plain object as the last argument. Per-call attributes override the default attributes.

>**NOTE:** You should never allow untrusted input to set the cookie attributes or you might be exposed to a [XSS attack](https://github.com/js-cookie/js-cookie/issues/396).

<a name="cookie-attributes-expires"></a>
#### expires

Defines when the cookie will be removed. Value must be a [`Number`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number) which will be interpreted as days from time of creation or a [`Date`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) instance. If omitted, the cookie becomes a session cookie.

To create a cookie that expires in less than a day, you can check the [FAQ on the Wiki](https://github.com/js-cookie/js-cookie/wiki/Frequently-Asked-Questions#expire-cookies-in-less-than-a-day).

**Default:** Cookie is removed when the user closes the browser.

**Examples:**

```js
Snowboard.cookie().set('name', 'value', { expires: 365 })
Snowboard.cookie().get('name') // => 'value'
Snowboard.cookie().remove('name')
```

<a name="cookie-attributes-path"></a>
#### path

A [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) indicating the path where the cookie is visible.

**Default:** `/`

**Examples:**

```js
Snowboard.cookie().set('name', 'value', { path: '' })
Snowboard.cookie().get('name') // => 'value'
Snowboard.cookie().remove('name', { path: '' })
```

<a name="cookie-attributes-domain"></a>
#### domain

A [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) indicating a valid domain where the cookie should be visible. The cookie will also be visible to all subdomains.

**Default:** Cookie is visible only to the domain or subdomain of the page where the cookie was created

**Examples:**

Assuming a cookie that is being created on `example.com`:

```js
Snowboard.cookie().set('name', 'value', { domain: 'subdomain.example.com' })
Snowboard.cookie().get('name') // => undefined (need to read at 'subdomain.example.com')
```

<a name="cookie-attributes-secure"></a>
#### secure

Either `true` or `false`, indicating if the cookie transmission requires a secure protocol (https).

**Default:** No secure protocol requirement.

**Examples:**

```js
Snowboard.cookie().set('name', 'value', { secure: true })
Snowboard.cookie().get('name') // => 'value'
Snowboard.cookie().remove('name')
```

<a name="cookie-attributes-sameSite"></a>
#### sameSite

A [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String), allowing to control whether the browser is sending a cookie along with cross-site requests.

Default: not set.

>**NOTE:** More recent browsers are making "Lax" the default value even without specifiying anything here.

**Examples:**

```js
Snowboard.cookie().set('name', 'value', { sameSite: 'strict' })
Snowboard.cookie().get('name') // => 'value'
Snowboard.cookie().remove('name')
```

<a name="cookie-attributes-defaults"></a>
#### Setting up defaults

In order to set global defaults that are used for every cookie that is created with the `Snowboard.cookie().set('name', 'value')` method you can call the `setDefaults(options)` method on the Cookie plugin and it will set the provided options as the global defaults.

If you want to get the current defaults, call `Snowboard.cookie().getDefaults()`.

```js
Snowboard.cookie().setDefaults({ path: '/', domain: '.example.com' });
Snowboard.cookie().set('example', 'value');
```

<a name="cookie-events"></a>
### Events

The Cookie plugin provides the ability to interact with cookies and modify their values during accessing or creating.

<a name="cookie-event-get"></a>
#### `cookie.get`

This event runs during `Snowboard.cookie().get()` and provides the `(string) name` & `(string) value` parameters, along with a callback method that can be used by a plugin to override the cookie value programatically. This can be used to manipulate or decode cookie values.

```js
class CookieDecryptor extends Singleton
{
    listens() {
        return {
            'cookie.get': 'decryptCookie',
        }
    }

    decryptCookie(name, value, setValue) {
        if (name === 'secureCookie') {
            setValue(decrypt(value));
        }
    }
}
```

<a name="cookie-event-set"></a>
#### `cookie.set`

This event runs during `Snowboard.cookie().set()` and provides the `(string) name` & `(string) value` parameters, along with a callback method that can be used by a plugin to override the value saved to the cookie programatically. This will allow you to manipulate or encrypt cookie values before storing them with the browser.

```js
class CookieEncryptor extends Singleton
{
    listens() {
        return {
            'cookie.set': 'encryptCookie',
        }
    }

    encryptCookie(name, value, setValue) {
        if (name === 'secureCookie') {
            setValue(encrypt(value));
        }
    }
}
```

<a name="json-parser"></a>
## JSON Parser

The JSON Parser utility is used to safely parse JSON-like (JS-object strings) data that does not strictly meet the JSON specifications. It is especially useful for parsing the values provided in the `data-request-data` attribute used by the [Data Attributes](data-attributes) functionality.

This is somewhat similar to [JSON5](https://json5.org/) or [RJSON](http://www.relaxedjson.org/), but not exactly. The key aspect is that it allows for data represented as a JavaScript Object in string form as if it was actively running JS to be parsed without the use of `eval()` which could cause issues with Content Security Policies that block the use of `eval()`.

>**NOTE:** Although this functionality is documented it is unlikely that regular developers will ever have need of interacting with this feature.

### Usage:

```js
let data = "key: value, otherKey: 'other value';
let object = Snowboard.jsonParser().parse(`{${data}}`);
```

<a name="sanitizer"></a>
## Sanitizer

The Sanitizer utility is a client-side HTML sanitizer designed mostly to prevent self-XSS attacks. Such an attack could look like a user copying content from a website that uses clipboard injection to hijack the values actually stored in the clipboard and then having the user paste the content into an environment where the content would be treated as HTML, typically in richeditor / WYSIWYG fields.

The sanitizer utility will strip all attributes that start with `on` (usually JS event handlers as attributes, i.e. `onload` or `onerror`) or contain the `javascript:` pseudo protocol in their values.

It is available both as a global function (`wnSanitize(html)`) and as a Snowboard plugin.

The following example shows how the Froala WYSIWYG editor can be hooked into to protect against a clipboard injection / self-XSS attack.

```js
$froalaEditor.on('froalaEditor.paste.beforeCleanup', function (ev, editor, clipboard_html) {
    return Snowboard.sanitizer().sanitize(clipboard_html);
});
```