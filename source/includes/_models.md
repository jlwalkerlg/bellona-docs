# Models

Bellona uses the active record pattern, wherein models are classes whose instances represent a single record in a database. Each column in the database for that record maps to a property on the model instance. The model instance can also have methods which interact with or provide information about the corrseponding database record.

## Creating models

```php
<?php

namespace App\Models;

use Bellona\Database\Model;

class Post extends Model
{
  protected $table = 'posts';
  protected $fillable = ['author_id', 'title', 'body'];
}
```

To create a model, write a new class in the `App\Models` namespace which extends `Bellona\Database\Model`. Each model requires a protected `$table` property, which is the name of the table in your database for which your model represents records. Additionally, each model requires a protected `$fillable` property if it is to be used to insert/update database records. This should be an array of column names in your database table, listing only the columns which you want to allow your model to update, either with new records or as updates to existing ones. This provides some security against updating a column which you did not intend, either accidentally or through a malicious user.

```php
<?php

namespace App\Models;

use Bellona\Database\Model;

class Post extends Model
{
  protected $table = 'posts';
  protected $fillable = ['author_id', 'title', 'body'];
  protected $primaryKey = 'post_id';
}
```

<aside class="notice">
  By default Bellona will assume that the primary key for your table is `id`. You can override this by specifying a protected `$primaryKey` property.
</aside>

## Using models

```php
<?php

// Save a new post to the database
$post = new App\Models\Post;
$post->author_id = 1;
$post->title = 'Post Title.';
$post->body = 'Post body...';
$post->save();

// Update an existing post.
$post->body = 'Updated post body...';
$post->save();

// Delete an existing post.
$post->delete();
```

To save a model instance to the database, use the `Bellona\Database\Model::save()` instance method. If the model has a property corresponding to the relevant table's primary key, Bellona will update the associated record in the database with all your model instance's properties that are listed in the `$fillable` array. If the model does not have a primary key, it will create a new record in the database and assign the inserted primary key to your model instance.

To delete a record associated with a model instance, use the `Bellona\Database\Model::delete()` instance method on your model instance.

You can also use the `Bellona\Database\Model::assign()` method to assign multiple values to properties on your model instance at once.

<aside class="notice">
  Only those properties which are listed in the <code>$fillable</code> array will be assigned.
</aside>

```php
<?php

use App\Models\Post;

// Find the post with primary key = 1
$post = Post::find(1);

// Find the posts with primary key = 1 or 2 or 3
$post = Post::find([1, 2, 3]);

// Insert a new post
$post = Post::create(['author_id' => 1, title => 'Post Title.', body => 'Post body...']);

// Insert multiple new posts
$post = Post::create([
  ['author_id' => 1, title => 'Post Title.', body => 'Post body...'],
  ['author_id' => 1, title => 'Post Title 2.', body => 'Post body...'],
  ['author_id' => 2, title => 'Post Title 3.', body => 'Post body...'],
]);

// Delete a single post
Post::delete(1);

// Delete multiple posts
Post::delete([1, 2, 3]);

```

Extending `Bellona\Database\Model` will provide three additional methods which can be called statically: `Bellona\Database\Model::find()`, `Bellona\Database\Model::create()`, and `Bellona\Database\Model::destroy()`.

```php
<?php

// Get all posts
$posts = App\Models\Post::get();

// Get 5 posts where author_id = 1
$posts = App\Models\Post::where('id', 1)->limit(5)->get();
```

Additionally, extending `Bellona\Database\Model` gives you access to all of the `Bellona\Database\QueryBuilder` instance methods as static methods on your `Model` class. By default, any records returned using these methods will return instances of your `Model`.
