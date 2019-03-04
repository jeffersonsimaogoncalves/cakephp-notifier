# Notifier plugin for CakePHP

This plugin allows you to integrate a simple notification system into your application. 

## Installation

You can install this plugin into your CakePHP application using [composer](http://getcomposer.org).

The recommended way to install this plugin as composer package is:

```
    composer require jeffersonsimaogoncalves/cakephp-notifier
```

Now load the plugin via the following command:

```
    bin/cake plugin load -b JeffersonSimaoGoncalves/Notifier
```

After loading the plugin you need to migrate the tables for the plugin using:

```
    bin/cake migrations migrate -p JeffersonSimaoGoncalves/Notifier
```

## Sending notifications

#### Templates

Before sending any notification, we need to register a template. An example about how to add templates:

```php
    $notificationManager->addTemplate('newBlog', [
        'title' => 'New blog by :username',
        'body' => ':username has posted a new blog named :name'
    ]);
```

When adding a new template, you have to add a `title` and a `body`. Both are able to contain variables like `:username`
and `:name`. Later on we will tell more about these variables.

#### Notify

Now we will be able to send a new notification using our `newBlog` template.

```php
    $notificationManager->notify([
        'users' => ['7be18ce3-94e6-476a-870d-a0108d1697b7', 'c72da51b-16c8-4741-8646-2e45721b3277'],
        'recipientLists' => ['administrators'],
        'template' => 'newBlog',
        'vars' => [
            'username' => 'Bob Mulder',
            'name' => 'My great new blogpost'
        ]
    ]);
```

> Note: You are also able to send notifications via the component: `$this->Notifier->notify()`.

With the `notify` method we sent a new notification. A list of all attributes:

- `users` - This is an integer or array filled with id's of users to notify. So, when you want to notify user c72da51b-16c8-4741-8646-2e45721b3277 and
7be18ce3-94e6-476a-870d-a0108d1697b7, add `[c72da51b-16c8-4741-8646-2e45721b3277, 7be18ce3-94e6-476a-870d-a0108d1697b7]`.
- `recipientLists` - This is a string or array with lists of recipients. Further on you can find more about
RecipientLists.
- `template` - The template you added, for example `newBlog`.
- `vars` - Variables to use. In the template `newBlog` we used the variables `username` and `name`. These variables can
be defined here.

#### Recipient Lists

To send notifications to large groups you are able to use RecipientLists.
You can register them with:

```php
    $notificationManager->addRecipientList('administrators', ['7be18ce3-94e6-476a-870d-a0108d1697b7', 'c72da51b-16c8-4741-8646-2e45721b3277','7be18ce3-94e6-476a-870d-a0108d1697b4', 'c72da51b-16c8-4741-8646-2e45721b3272']);
```
    
Now we have created a list of recipients called `administrators`.

This can be used later on when we send a new notification: 

```php
    $notificationManager->notify([
        'recipientLists' => ['administrators'],
    ]);
```

Now, the users 7be18ce3-94e6-476a-870d-a0108d1697b7, c72da51b-16c8-4741-8646-2e45721b3277, 7be18ce3-94e6-476a-870d-a0108d1697b4 and c72da51b-16c8-4741-8646-2e45721b3272 will receive a notification.

## Retrieving notifications

#### Lists

You can easily retrieve notifications via the `getNotifications` method. Some examples:

```php
    // getting a list of all notifications of the current logged in user
    $this->Notifier->getNotifications();

    // getting a list of all notifications of the user with id c72da51b-16c8-4741-8646-2e45721b3272
    $this->Notifier->getNotifications('c72da51b-16c8-4741-8646-2e45721b3272');
    
    // getting a list of all unread notifications
    $this->Notifier->allNotificationList('c72da51b-16c8-4741-8646-2e45721b3272', true);

    // getting a list of all read notifications
    $this->Notifier->allNotificationList('c72da51b-16c8-4741-8646-2e45721b3272', false);
```

#### Counts

Getting counts of read/unread notifications can be done via the `countNotifications` method. Some examples:

```php
    // getting a number of all notifications of the current logged in user
    $this->Notifier->countNotifications();

    // getting a number of all notifications of the user with id c72da51b-16c8-4741-8646-2e45721b3272
    $this->Notifier->countNotifications('c72da51b-16c8-4741-8646-2e45721b3272');
    
    // getting a number of all unread notifications
    $this->Notifier->countNotificationList('c72da51b-16c8-4741-8646-2e45721b3272', true);

    // getting a number of all read notifications
    $this->Notifier->countNotificationList('c72da51b-16c8-4741-8646-2e45721b3272', false);
```

#### Mark as read

To mark notifications as read, you can use the `markAsRead` method. Some examples:

```php
    // mark a single notification as read
    $this->Notifier->markAsRead(500);

    // mark all notifications of the given user as read
    $this->Notifier->markAsRead(null, 'c72da51b-16c8-4741-8646-2e45721b3272');
```

#### Notification Entity

The following getters can be used at your notifications entity:
- `title` - The generated title including the variables.
- `body` - The generated body including the variables.
- `unread` - Boolean if the notification is not read yet.
- `read` - Boolean if the notification is read yet.

Example:
    
```php
    // returns true or false
    $entity->get('unread');
    
    // returns the full output like 'Bob Mulder has posted a new blog named My Great New Post'
    $entity->get('body');
```

#### Passing to view

You can do something like this to use the notification list in your view:

```php
    $this->set('notifications', $this->Notifier->getNotifications());
```

## Notification Manager

The `NotificationManager` is the Manager of the plugin. You can get an instance with:

```php
    NotificationManager::instance();
```

The `NotificationManager` has the following namespace: `JeffersonSimaoGoncalves\Notifier\Utility\NotificationManager`.

The manager has the following methods available:

- `notify`
- `addRecipientList`
- `getRecipientList`
- `addTemplate`
- `getTemplate`

## Notifier Component

The `JeffersonSimaoGoncalves/Notifier.Notifier` component can be used in Controllers:

```php
    public function initialize()
    {
        parent::initialize();
        $this->loadComponent('JeffersonSimaoGoncalves/Notifier.Notifier');
    }
```

The component has the following methods available:

- `getNotifications`
- `countNotifications`
- `markAsRead`
- `notify`

## Keep in touch

Pull Requests are always more than welcome!

## Credits

This work is based on the [code by Bakkerij](https://github.com/bakkerij/notifier).
