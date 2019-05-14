# Authorization

Authorization is the process by which you ensure that the user has the permisson to perform a given action on a specific database model, for example creating a new post or editing an existing one.

In Bellona, this is done by creating policies.

## Creating policies

> Create a policy for editing a post:

```php
<?php

namespace App\Policies;

use App\Models\User;
use App\Models\Post;

class PostPolicy
{
  public function edit(User $user, Post $post)
  {
    return $user->id === $post->user_id;
  }
}
```

> Use the policy to authorize user permissions:

```php
<?php

Router::put('/posts/{post}', function(App\Models\Post $post) {
  if (can('edit', $post)) {
    // Logged in user is authorized to edit the post...
  }
});
```

> Alternatively, use the `App\Middleware\Authorize` middleware:

```php
<?php

Router::put('/posts/{post}', function(App\Models\Post $post) {
  // Logged in user is authorized to edit the post...
})->middleware('can:edit,post');
```

To create a `Policy`, create a new class in the `App\Policies` namespace with the name `<Model>Policy` where `<Model>` is the name of the model for which your policies apply. For example, to write any number of policies for the `App\Models\Post` model, create a `App\Policies\PostPolicy` class.

Next, create a public method to authorize a specific action. This method should return true only if the user is authorized; otherwise, return false.

Then, use the `Bellona\Auth\Authorization::can()` method, or the `can()` helper to check whether or not the user is authorized to perform said action.

`Bellona\Auth\Authorization::can()` accepts three arguments: `$action`, `$model`, and `$user`. `$action` is the name of the function to call in your `Policy` class. `$model` is either the model instance for which to authorize permissions, or, if a specific instance is not required, the name of the model's class name as a string (e.g. for creating a new `App\Models\Post`). `$user` is optional and is the specific user to authorize. If no `$user` is given, the currently logged in user will be used instead.
