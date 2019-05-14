# Controllers

Controllers are classes which handle incoming requests, usually house much of the necessary logic, communicate with a `Model` to fetch or update data, and return a response in the form of a `View`.

## Creating controllers

> Define a controller and an action to display all posts:

```php
<?php

namespace App\Controllers;

use Bellona\Http\Controller;

class Posts extends Controller
{
  public function show()
  {
    // Display all posts...
  }
}
```

> Register the controller callback with a route:

```php
<?php

Router::get('/posts', 'Posts@show');
```

To create a controller, simply write a new class in the `App\Controllers` namespace and extend the `Bellona\Http\Controller`. You can then define methods to respond to the incoming request and register a route to call one of your controller's methods when it matches the incoming URL.

Extending `Bellona\Http\Controller` gives you access to the `Bellona\Http\Controller::middleware()` method, which works the same as the `Bellona\Http\Route::middleware()` method, except that the middleware is defined for all methods within your controller.
