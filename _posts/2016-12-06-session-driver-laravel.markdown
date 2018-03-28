---
title: Custom session driver for Laravel
layout: post
image: /assets/images/ilze-lucero-61471-unsplash.jpg
headerImage: true
tag:
- PHP
- Laravel
category: blog
author: pete
title: How to build a custom session driver for Laravel 5
description: An article about using a custom session driver to manage deleting transient entities in a laravel 5.3 application. Photo by Ilze Lucero on Unsplash
---

*Originally written for and posted on [Foundry](https://insidefoundry.co.uk/2016/12/06/custom-laravel-session-driver.html)*

[Photo by Ilze Lucero on Unsplash](https://unsplash.com/photos/l_VVd8nV3PE)

For a recent project built using the PHP framework [Laravel](https://laravel.com/) a requirement was the ability to have certain resources be removed when the current session expires.

Using the built-in database session driver we maintain a pivot table linking Entities in a transient state to the current session. Later, if the current session logs in or registers an account we can store the processed entities against that account and remove them from the transients table, disassociating them from the current session.

This was brilliant and worked flawlessly-

![Snippet of error screen during Laravel development](/assets/images/insights/laravel-session-driver-oops.png)

\-until the session garbage collection attempted to clean up some old sessions and didn't know to remove their related models.

This garbage collection, which can be triggered at any time (with a default probability of 2 in 100 requests) will affect another user's session if it causes an error. In order to prevent any foreign key errors from occurring we need to ensure that all would-be orphaned records are removed before the session itself is destroyed.

To accomplish this, we devised a custom session handler to extend the provided database session handler.

{% gist wadtech/74b4363bf065245a2ef055dea6adfc72 AppDatabaseSessionHandler.php %}

Registering the `AppDatabaseSessionHandler` is done through a provider, which is set up the same way as the built-in `DatabaseSessionHandler`.

{% gist wadtech/74b4363bf065245a2ef055dea6adfc72 SessionProvider.php %}

For our application, it relies on this functionality so it's fine to hard-code the config into the app. Otherwise we would edit our `.env` files. To make it a simpler default for the application, editing it in `.env.example` would be a good idea too.

{% gist wadtech/74b4363bf065245a2ef055dea6adfc72 session.php %}

Now when a session expires, it transparently cleans up any model that has not been committed to permanent storage. Neat!

For an alternative approach, and one that may become more valuable once the project increases in scope, would be to leverage queuing and implement a garbage collection job that looks for orphaned records and removes them from the database.
