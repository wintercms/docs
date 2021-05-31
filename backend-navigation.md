- [Introduction](#introduction)

<a name="introduction"></a>
## Introduction

The navigation in the Backend, by default, is comprised of two separate navigation elements:

- **The main navigation bar:** Located horizontally at the top of the page, this contains the main entry links to installed modules and plugins.
- **The side navigation menu:** Located vertically to the left of the page, this is a contextual menu showing the sub-navigation of the currently viewed module or plugin.

Together, these two elements form the primary way of traversing the many functions and pages of a Winter CMS Backend instance.

The bulk of menu management in Winter CMS is handled by the [NavigationManager](/docs/api/develop/Backend/Classes/NavigationManager.html) class, which provides the capabilities to register and retrieve main menu and side menu items, determine the current active page and customize the look and feel of the navigation.

<a name="registering-menu-items"></a>
## Registering menu items

The most common way of registering menu items is through a plugin, to provide the necessary access points for your plugin's functionality. Plugins can extend the back-end navigation menus by defining the `registerNavigation` method in their [Plugin registration class](../plugin/registration#navigation-menus).

To define menu items outside of this method, you may use the [`addMainMenuItem`](/docs/api/develop/Backend/Classes/NavigationManager.html#method_addMainMenuItem) and [`addSideMenuItem`](/docs/api/develop/Backend/Classes/NavigationManager.html#addSideMenuItem) methods of the `NavigationManager` instance.

```php
/**
 * Add a main menu item
 */
NavigationManager::instance()->addMainMenuItem(
    'Acme.Demo', // The plugin's code
    'demo',      // A unique identifier for the menu item
    [            // The navigation item settings
        'label'       => 'Demo',
        'url'         => Backend::url('acme/demo/demo'),
        'icon'        => 'icon-star',
        'order'       => 400
        'permissions' => [
            'acme.demo.read',
            'acme.demo.write',
        ],
        // To show a counter (eg. to show new messages), you can use the following
        // settings
        'counter'      => ['\Acme\Demo\Classes\DemoCounterService', 'getDemoCount'],
        'counterLabel' => 'Label describing the counter content',
        // Optionally, you can instead show a badge instead of a counter, which can
        // show some text instead.
        'badge'        => 'New'
    ]
);

/**
 * Add a side menu item
 */
NavigationManager::instance()->addSideMenuItem(
    'Acme.Demo', // The plugin's code
    'demo',      // The identifier of a main menu item that this side menu item will display for.
    'content',   // A unique identifier for the side menu item
    [            // The navigation item settings
        'label'       => 'Content',
        'icon'        => 'icon-pencil',
        'url'         => Backend::url('acme/demo/content'),
        'permissions' => []
    ]
);
```