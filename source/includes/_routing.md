# Routing

## Defining routes

```php
<?php

// Register a route to listen to GET requests to the /about endpoint,
// and handle the response with an anonymous function:
Router::get('/about', function() {
  echo 'This is the about page.';
});

// Register a route to listen to POST requests to the /about endpoint,
// and handle the response in the about method of the Home controller:
Router::post('/about', 'Home@about');

// PUT, PATCH, and DELETE routes work in the same way:
Router::put('/posts', 'Posts@update');
Router::patch('/posts', 'Posts@edit');
Router::delete('/posts', 'Posts@delete');

// Listen for all HTTP verbs:
Router::all('/users', function() {
  // ...
});

// Listen for multiple specific verbs:
Router::match(['PUT', 'PATCH'], '/users', function() {
  // ...
});

```

All of your routes should be registered within the `routes` directory. If you wish to register an API route, do so within the `routes/api.php` file; otherwise, use the `routes/web.php` file.

Routes for each verb can be defined by calling the relevent method on the `Bellona\Http\Router`. Note that an alias has been set up for the `Bellona\Support\Facades\Router` facade, so that you do not have to use the full namespace within your route files.

The `Bellona\Http\Router` has a number of methods pertaining to each HTTP verb you wish to register a route for. For example, the `Bellona\Http\Router::get()` is used to registed a route for GET requests to a specific endpoint and accepts two parameters: a URL defining the endpoint to which the route should be registered, and a callback.

The callback can either be anonymous function to run if the route matches the incoming request, or an action (method) to call on a specific controller in the format `'ControllerName@action'`. Bellona will look for your controller inside of the `app/controllers` directory. If it is nested within a subdirectory thereof, simply specify its namespace relative to the `app/controllers` directory like so: `Path\To\Class`.

In addition to a `get()` method on the `Bellona\Http\Router`, there is also a `post()`, `put()`, `patch()`, and `delete()` method which all work in the same way.

If you wish to assign a route to all HTTP verbs, you can use the `Bellona\Http\Router::all()` method, which accepts the same parameters as `Bellona\Http\Router::get()`.

If you wish to assign a route to multiple HTTP verbs at once (but not all), you can use the `Bellona\Http\Router::match()` method, which accepts an array of verbs (`'GET'`, `'POST'`, `'PUT'`, `'PATCH'`, or `'DELETE'`) as the first argument, in addition to the URL and the callback as second and third arguments respectively.

An individual `Bellona\Http\Route` instance is returned by each of these methods, allowing your to chain on calls to set middleware and custom regex patterns if you wish.

<aside class="notice">
  The order in which you define your routes is important, since only the first matched route will be called.
</aside>

## Route parameters

```php
<?php

Router::get('/posts/{id}', function($id) {
  // Display the post with id = $id...
});

Router::get('/posts/author/{id}/page/{page}', function($authorId, $pageNumber) {
  // Display the $pageNumber'th page of all posts by author with id = $authorID...
});

```

Rather than specifying exact URL endpoints, you can also specify a parameter using curly braces. These parameters will be automatically passed to your callback in the order they are defined. Therefore, the name of your callback arguments which are not type-hinted do not have to match the name of the parameters defined in the URL.

### Custom regex

```php
<?php

// Ensure the {id} parameter only matches numbers.
Router::get('/posts/{id}', 'Posts@show')->where('id', '[0-9]+');

// Ensure both the {id} and the {page} parameters only match numbers.
Router::get('/posts/author/{id}/page/{page}', 'Posts@author')->where([
  'id' => '[0-9]+',
  'page' => '[0-9]+'
]);

```

By default each parameter in your URL is replaced with the simple regex pattern `(\w+)`. If you wish to impose your own regex, for example to ensure an `{id}` parameter only matches numbers, you can do so with the `Bellona\Http\Route::where()` instance method.

## Dependency injection & implicit model binding

```php
<?php

Router::get('/posts/{post}', function(Bellona\Http\Request $request, App\Models\Post $post) {
  // $request will be the resolved Bellona\Http\Request instance.
  // $post will be the App\Models\Post whose primary key is {post}.
});

```

The `Bellona\Http\Router` will attempt to resolve any type-hinted parameters specified in your callback before calling it. If the callback parameter has the same name as one of your URL parameters, it will assume you are expecting a database record and will attempt to find it according to its primary key, where the model it will look for is determined by the name of the type-hinted class, and where the primary key is the value found in the URL. This is implicit model binding. Otherwise, it will assume you wish to resolve a service from the service container and attempt to do so automatically. This is dependency injection.

<aside class="notice">
  If a type-hinted parameter can't be resolved, or the incoming request URL does not match any registered route, Bellona with exit with a 404 response. The 404 page displayed to the user can be modified by editing the <code>app/views/404.php</code> file.
</aside>
