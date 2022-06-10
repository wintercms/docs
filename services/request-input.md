# Requests & Input

## Basic input

You may access all user input with a few simple methods. You do not need to worry about the HTTP verb for the request when using the `Input` facade, as input is accessed in the same way for all verbs. The global `input` [helper function](../services/helpers) is an alias for `Input::get`.

#### Retrieving an input value

```php
$name = Input::get('name');
```

#### Retrieving a default value if the input value is absent

```php
$name = Input::get('name', 'Sally');
```

#### Determining if an input value is present

```php
if (Input::has('name')) {
    //
}
```

#### Getting all input for the request

```php
$input = Input::all();
```

#### Getting only some of the request input

```php
$input = Input::only('username', 'password');

$input = Input::except('credit_card');
```

When working on forms with "array" inputs, you may use dot notation to access the arrays:

```php
$input = Input::get('products.0.name');
```

> **NOTE:** Some JavaScript libraries such as Backbone may send input to the application as JSON. You may access this data via `Input::get` like normal.

## Cookies

By default, all cookies created by the Winter are encrypted and signed with an authentication code, meaning they will be considered invalid if they have been changed by the client. Cookies named in the `cookie.unencryptedCookies` config key will not be encrypted.

> **NOTE:** Cookies are encrypted with the APP_KEY, so there is the potential for cookies to be crafted by the client if they know the APP_KEY. If your application's encryption key is in the hands of a malicious party, that party could craft cookie values using the encryption key and exploit vulnerabilities inherit to PHP object serialization / unserialization, such as calling arbitrary class methods within your application. To mitigate that, always rotate your APP_KEY if you suspect it has been compromised and ensure you always verify that the data you received from a cookie is what you expect it to be before using it.

#### Retrieving a cookie value

```php
$value = Cookie::get('name');
```

#### Attaching a new cookie to a response

```php
$response = Response::make('Hello World');

$response->withCookie(Cookie::make('name', 'value', $minutes));
```

#### Queueing a cookie for the next response

If you would like to set a cookie before a response has been created, use the `Cookie::queue` method. The cookie will automatically be attached to the final response from your application.

```php
Cookie::queue($name, $value, $minutes);
```

#### Creating a cookie that lasts forever

```php
$cookie = Cookie::forever('name', 'value');
```

#### Handling cookies without encryption

If you don't want some cookies to be encrypted or decrypted, you can specify them in configuration.
This is useful, for example, when you want to pass data from frontend to server side backend via cookies, and vice versa.

Add names of the cookies that should not be encrypted or decrypted to `unencryptedCookies` parameter in the `config/cookie.php` configuration file.

```php
/*
|--------------------------------------------------------------------------
| Cookies without encryption
|--------------------------------------------------------------------------
|
| Winter CMS encrypts/decrypts cookies by default. You can specify cookies
| that should not be encrypted or decrypted here. This is useful, for
| example, when you want to pass data from frontend to server side backend
| via cookies, and vice versa.
|
*/

'unencryptedCookies' => [
    'my_cookie',
],
```

Alternatively for plugins, you can also add these dynamically from `Plugin.php` of your plugin.

```php
public function boot()
{
    Config::push('cookie.unencryptedCookies', "my_cookie");
}
```

## Old input

You may need to keep input from one request until the next request. For example, you may need to re-populate a form after checking it for validation errors.

#### Flashing input to the session

```php
Input::flash();
```

#### Flashing only some input to the session

```php
Input::flashOnly('username', 'email');

Input::flashExcept('password');
```

Since you often will want to flash input in association with a redirect to the previous page, you may easily chain input flashing onto a redirect.

```php
return Redirect::to('form')->withInput();

return Redirect::to('form')->withInput(Input::except('password'));
```

> **NOTE:** You may flash other data across requests using the [Session](../services/session) class.

#### Retrieving old data

```php
Input::old('username');
```

## Files

#### Retrieving an uploaded file

```php
$file = Input::file('photo');
```

#### Determining if a file was uploaded

```php
if (Input::hasFile('photo')) {
    //
}
```

The object returned by the `file` method is an instance of the `Symfony\Component\HttpFoundation\File\UploadedFile` class, which extends the PHP `SplFileInfo` class and provides a variety of methods for interacting with the file.

#### Determining if an uploaded file is valid

```php
if (Input::file('photo')->isValid()) {
    //
}
```

#### Moving an uploaded file

```php
Input::file('photo')->move($destinationPath);

Input::file('photo')->move($destinationPath, $fileName);
```

#### Retrieving the path to an uploaded file

```php
$path = Input::file('photo')->getRealPath();
```

#### Retrieving the original name of an uploaded file

```php
$name = Input::file('photo')->getClientOriginalName();
```

#### Retrieving the extension of an uploaded file

```php
$extension = Input::file('photo')->getClientOriginalExtension();
```

#### Retrieving the size of an uploaded file

```php
$size = Input::file('photo')->getSize();
```

#### Retrieving the MIME type of an uploaded file

```php
$mime = Input::file('photo')->getMimeType();
```

## Request information

The `Request` class provides many methods for examining the HTTP request for your application and extends the `Symfony\Component\HttpFoundation\Request` class. Here are some of the highlights.

#### Retrieving the request URI

```php
$uri = Request::path();
```

#### Retrieving the request method

```php
$method = Request::method();

if (Request::isMethod('post')) {
    //
}
```

#### Determining if the request path matches a pattern

```php
if (Request::is('admin/*')) {
    //
}
```

#### Get the request URL

```php
$url = Request::url();
```

#### Retrieve a request URI segment

```php
$segment = Request::segment(1);
```

#### Retrieving a request header

```php
$value = Request::header('Content-Type');
```

#### Retrieving values from $_SERVER

```php
$value = Request::server('PATH_INFO');
```

#### Determining if the request is over HTTPS

```php
if (Request::secure()) {
    //
}
```

#### Determine if the request is using AJAX

```php
if (Request::ajax()) {
    //
}
```

#### Determine if the request has JSON content type

```php
if (Request::isJson()) {
    //
}
```

#### Determine if the request is asking for JSON

```php
if (Request::wantsJson()) {
    //
}
```

#### Checking the requested response format

The `Request::format` method will return the requested response format based on the HTTP Accept header:

```php
if (Request::format() == 'json') {
    //
}
```
