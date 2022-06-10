# Backend Users & Permissions

The user management for the backend includes features like roles, groups, permissions, password resets and sign-in throttling. Plugins can also register permissions that control access to the features in the backend.

## Users and Permissions

Access to all parts of a Winter CMS instance is controlled by the Permissions system. At the lowest level, there are Super Users (users with the `is_superuser` flag set to true), Administrators (users) and permissions. The `\Backend\Models\User` models are the containers that hold all the important information about a user.

Superusers have access to everything in the system and are only manageable by themselves or other superusers; they are not visible to nor editable by regular administrators, not even if an administrator has the `backend.manage_users` permission.

Permissions are string keys in the form of `author.plugin.permission_name` that are granted to users by either direct assignment on their Edit Administrator page or by inheritance through the user's Role.

When checking if a user has a specific permission, the permission settings for that user's role are inherited and then overridden by any permissions applied directly to that user. For example, if user **Bob** has role **Genius**, and role **Genius** has the `eat_cake` permission, but **Bob** has the `eat_cake` permission specifically set to deny then **Bob** will not get to `eat_cake`. However, if **Bob** has the permission `eat_vegetables` assigned directly to him, but the **Genius** role does not, then **Bob** still gets to `eat_vegetables`.

Roles (`\Backend\Models\UserRole`) are groupings of permissions with a name and description used to identify the role. An Administrator can only have one role assigned to them at once. A Role could be assigned to multiple administrators. Winter ships with two system roles by default, `developer` and `publisher`. Any number of custom roles with their own combinations of permissions can be created and applied to users.

> **NOTE:** Any user with the `manage_users` permissions can manage the assignment of roles, but only to other users (not to themselves), and roles can only be created or modified by a superuser.

> **NOTE:** System roles (`developer`, `publisher`, and any role with `is_system` set to `true`) cannot have their permissions changed through the backend. They are assumed to have access to all permissions, unless a given permission specifies a specific role or roles that it applies to using the `roles` array key in the definition of the permission (in which case only that specified system role has access to it).

Groups (`\Backend\Models\UserGroup`) are an organizational tool for grouping administrators, they can be thought of as "user categories". They have nothing to do with permissions and are strictly for organizational purposes. For instance, if you wanted to send an email to all users that are in the group `Head Office Staff`, you would simply do `Mail::sendTo(UserGroup::where('code', 'head-office-staff')->get()->users, 'author.plugin::mail.important_notification', $data);`

## Backend user helper

The global `BackendAuth` facade can be used for managing administrative users, which primarily inherits the `Winter\Storm\Auth\Manager` class. To register a new administrator user account, use the `BackendAuth::register` method.

```php
$user = BackendAuth::register([
    'first_name' => 'Some',
    'last_name' => 'User',
    'login' => 'someuser',
    'email' => 'some@website.tld',
    'password' => 'changeme',
    'password_confirmation' => 'changeme'
]);
```

The `BackendAuth::check` method is a quick way to check if the user is signed in. To return the user model that is signed in, use `BackendAuth::getUser` instead. Additionally, the active user will be available as `$this->user` inside any [backend controller](../backend/controllers-ajax).

```php
// Returns true if signed in.
$loggedIn = BackendAuth::check();

// Returns the signed in user
$user = BackendAuth::getUser();

// Returns the signed in user from a controller
$user = $this->user;
```

You may look up a user by their login name using the `BackendAuth::findUserByLogin` method.

```php
$user = BackendAuth::findUserByLogin('someuser');
```

You may authenticate a user by providing their login and password with `BackendAuth::authenticate`. You can also authenticate as a user simply by passing the `Backend\Models\User` model along with `BackendAuth::login`.

```php
// Authenticate user by credentials
$user = BackendAuth::authenticate([
    'login' => post('login'),
    'password' => post('password')
]);

// Sign in as a specific user
BackendAuth::login($user);
```

## Registering permissions

Plugins can register backend user permissions by overriding the `registerPermissions` method inside the [Plugin registration class](../plugin/registration#registration-file). The permissions are defined as an array with keys corresponding the permission keys and values corresponding the permission descriptions. The permission keys consist of the author name, the plugin name and the feature name. Here is an example code:

```
acme.blog.access_categories
```

The next example shows how to register backend permission items. Permissions are defined with a permission key and description. In the backend permission management user interface permissions are displayed as a checkbox list. Backend controllers can use permissions defined by plugins for restricting the user access to [pages](#page-access) or [features](#features).

```php
public function registerPermissions()
{
    return [
        'acme.blog.access_posts' => [
            'label' => 'Manage the blog posts',
            'tab'   => 'Blog',
            'order' => 200,
            'roles' => [\Backend\Models\UserRole::CODE_DEVELOPER, \Backend\Models\UserRole::CODE_PUBLISHER],
        ],
        // ...
    ];
}
```

When developing a plugin that will be used by more projects than just your own (i.e. published on the marketplace) it is highly recommended that you populate the `roles` property with either `\Backend\Models\UserRole::CODE_DEVELOPER` or `\Backend\Models\UserRole::CODE_PUBLISHER` or both depending on the level of access you would like users with those default system-provided roles to have to your plugin's permissions.

You can also provide the API codes (`$role->code`) of non-default roles, but note that doing that will automatically convert that role into a "system" role which means that only the permissions that are explicitly registered to it in code will be attached to it. System roles cannot have their permissions be edited through the backend interface or in the database. Avoid attaching your permissions to non-default system roles (i.e. `CODE_DEVELOPER` & `CODE_PUBLISHER`) if you will be publishing the plugin on the marketplace or otherwise making it available for use in other projects that may not have your custom roles defined (unless your plugin itself provides said custom role via a seeder run during the migration process).

>**NOTE:** If the `roles` property isn't provided then the only users that will have access to the permission by default will be superusers or users with the `\Backend\Models\UserRole::CODE_DEVELOPER` role which inherits all "orphaned permissions" (permissions without any roles specified).

## Restricting access to backend pages

In a backend controller class you can specify which permissions are required for access the pages provided by the controller. It's done with the `$requiredPermissions` controller's property. This property should contain an array of permission keys. If the user permissions match any permission from the list, the framework will let the user to see the controller pages.

```php
<?php namespace Acme\Blog\Controllers;

use Backend\Classes\BackendController;

class Posts extends BackendController
{
    public $requiredPermissions = ['acme.blog.access_posts'];
}
```

You can also use the **asterisk** symbol to indicate the "all permissions" condition. In the next example the controller pages are accessible for all users who has any permissions starting with the "acme.blog." string:

```php
public $requiredPermissions = ['acme.blog.*'];
```

## Restricting access to features

The backend user model has methods that allow to determine whether the user has specific permissions. You can use this feature in order to limit the functionality of the backend user interface. The permission methods supported by the backend user are `hasAccess` and `hasPermission`. Both methods take two parameters: the permission key string (or an array of key strings) and an optional parameter indicating that all permissions listed with the first parameters are required.

The `hasAccess` method returns **true** for any permission if the user is a superuser (`is_superuser` set to `true`). The `hasPermission` method is more strict, only returning true if the user actually has the specified permissions either in their account or through their role. Generally, `hasAccess` is the preferred method to use as it respects the absolute power of the superuser. The following example shows how to use the methods in the controller code:

```php
if ($this->user->hasAccess('acme.blog.*')) {
    // ...
}

if ($this->user->hasPermission([
    'acme.blog.access_posts',
    'acme.blog.access_categories'
])) {
    // ...
}
```

You can also use the methods in the backend views for hiding user interface elements. The next examples demonstrates how you can hide a button on the Edit Category [backend form](../backend/forms):

```php
<?php if ($this->user->hasAccess('acme.blog.delete_categories')): ?>
    <button
        type="button"
        class=wn-icon-trash-o btn-icon danger pull-right"
        data-request="onDelete"
        data-load-indicator="Deleting Category..."
        data-request-confirm="Do you really want to delete this category?">
    </button>
<?php endif ?>
```
