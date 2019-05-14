# Middleware

## Creating middleware

```php
<?php

namespace App\Middleware;

class Admin
{
  public function run($request)
  {
    // Ensure user is logged in and is an admin user,
    // otherwise redirect...
  }
}
```

To create middleware, you should create a class in the `App\Middleware` namespace and define a `run()` method. This will be called automatically before the callback for your matching route, allowing you to easily implement authentication, authorization, CSRF protection, etc. The `Bellona\Http\Request` instance will automatically be passed as the first parameter.

## Using middleware

```php
<?php

// Specify the 'auth' middleware to run before allowing a user to create a new post.
Router::post('/posts/{post}', 'Posts@create')->middleware('auth');
```

> The `auth` middleware should be mapped to the name of the corrseponding middleware class in your `config/middleware.php` array:

```php
<?php

return [
  'auth' => App\Middleware\Authenticate::class,
  // ...
];

```

You can specify middleware for an individual route with the `Bellona\Http\Route::middleware()` instance method. This accepts any number of strings of the format `name:arg1,arg2...`, where `name` is the name of the middleware specified as a key in your `config/middleware.php` file, and `arg1` and `arg2` are any arguments required by your middleware. If no arguments are required, simply exclude them (leaving just `name`).

If you wish to define middleware for every method in your controller, you can do so using the `Bellona\Http\Controller::middleware()` method, which works in the same way but applies to all methods in your controller.

## Implicit model binding

```php
<?php

Router::get('/posts/{post}', 'Posts@show')->middleware('posts:post');
```

> This will pass the URL parameter typed in by the user and captured in the {post} parameter into your middleware, where you can (optionally) type-hint the appropriate model so that it is passed in automatically:

```php
<?php

class PostsMiddleware
{
  public function run($request, App\Models\Post $post)
  {
    // Decide whether to allow access to the post or not...
  }
}
```

You can use implicit model binding by passing the name of the URL parameter corresponding to your model as an argument to your middleware and type-hinting the model in the middleware function definition.

## Available middleware

### CSRF

The `App\Middleware\CSRF` middleware is called automatically when the incoming request is anything other than a `GET` request. This will provide protection against CSRF attacks by using the `Bellona\Security\CSRF` class to require that a valid token by passed either as part of the request body, or as a `X-CSRF-TOKEN` header.

By default, if a valide token is not found, the middleware will flash an alert message to the session, save the request data, and redirect back to the previous page. You can modify this behaviour by modifying the `App\Middleware\CSRF::deny()` method.

### Authentication

> Ensure the user is logged in before allowing them to create a new post:

```php
<?php

Router::post('/posts', 'Posts@create')->middleware('auth');
```

The `App\Middleware\Authenticate` middleware uses the `Bellona\Auth\Authenticaiton` class to require that a user is logged in.

By default, if the user is not logged in, an alert message will be flashed to the session and redirect to the `URL_ROOT`. You can modify this behaviour by modifying the `App\Middleware\Authenticate::deny()` method.

### Authorization

> Ensure a user is authorized to edit a post:

```php
<?php

Router::put('/post/{post}', 'Posts@edit')->middleware('can:edit,post');
```

The `App\Middleware\Authorize` middleware requires that the user is logged in and is authorized to perform a given action on a specific model. The name of the action and the model should be passed in as arguments.

To perform this check, `App\Middleware\Authorize` uses the `can()` helper, which in turn calls the appropriate method on the appropriate `Policy`.
