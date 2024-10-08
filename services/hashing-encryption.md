# Hashing & Encryption

## Configuration

When you first installed Winter, a random key should have been generated for you. You can confirm this by checking the `key` option of your `config/app.php` configuration file, or the `APP_KEY` of your `.env` file. If the key remains unchanged, you should set it to a 32 character, random string. If this value is not properly set, all encrypted values will be insecure.

To set this key you can run

```bash
php artisan key:generate
```

## Hashing

The `Hash` facade provides secure Bcrypt hashing for storing user passwords. Bcrypt is a great choice for hashing passwords because its "work factor" is adjustable, which means that the time it takes to generate a hash can be increased as hardware power increases.

You may hash a password by calling the `make` method on the `Hash` facade:

```php
$user = new User;
$user->password = Hash::make('mypassword');
$user->save();
```

Alternatively, models can implement the [Hashable trait](../database/traits#hashable) to automatically hash attributes.

### Verifying a password against a hash

The `check` method allows you to verify that a given plain-text string corresponds to a given hash.

```php
if (Hash::check('plain-text', $hashedPassword)) {
    // The passwords match...
}
```

### Checking if a password needs to be rehashed

The `needsRehash` function allows you to determine if the work factor used by the hasher has changed since the password was hashed:

```php
if (Hash::needsRehash($hashed)) {
    $hashed = Hash::make('plain-text');
}
```

## Encryption

You may encrypt a value using the `Crypt` facade. All encrypted values are encrypted using OpenSSL and the `AES-256-CBC` cipher. Furthermore, all encrypted values are signed with a message authentication code (MAC) to detect any modifications to the encrypted string.

For example, we may use the `encrypt` method to encrypt a secret and store it on a [database model](../database/model):

```php
$user = new User;
$user->secret = Crypt::encrypt('shhh no telling');
$user->save();
```

### Decrypting a value

Of course, you may decrypt values using the `decrypt` method on the `Crypt` facade. If the value can not be properly decrypted, such as when the MAC is invalid, an `Illuminate\Contracts\Encryption\DecryptException` exception will be thrown:

```php
use Illuminate\Contracts\Encryption\DecryptException;

try {
    $decrypted = Crypt::decrypt($encryptedValue);
}
catch (DecryptException $ex) {
    //
}
```
