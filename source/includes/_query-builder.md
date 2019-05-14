# Query Builder

The `Bellona\Database\QueryBuilder` is based on [Laravel's query builder](https://laravel.com/docs/5.8/queries) and provides a number of methods to fluently create database queries.

<aside class="notice">
  Bellona provides a conveninent <code>Bellona\Support\Facades\DB</code> facade for accessing these methods statically.
</aside>

## Fetching multiple records with DB::get()

```php
<?php

$posts = DB::table('posts')->get(); // fetch all posts
$posts = DB::table('posts')->where('user_id', 10)->get(); // fetch all posts where user_id = 10
$posts = DB::table('posts')->where('user_id', '!=', 10)->get(); // fetch all posts where user_id != 10
$posts = DB::table('posts')->limit(20)->offset(50)->get(); // fetch 20 posts, starting from the 51st
$posts = DB::table('posts')->where('user_id', 10)->limit(10)->offset(10)->get(); // fetch 10 posts where user_id = 10, starting from the 11th
```

All results are returned as an array of object instances of the `stdClass` class.

<aside class="notice">
  The order of the query building does not matter, so long as <code>DB::table()</code> is first, and <code>DB::get()</code> is last.
</aside>

## Fetching a single record with DB::first()

```php
<?php

$posts = DB::table('posts')->first(); // fetch first post
```

This adds a `LIMIT 1` to the query and returns the record directly, rather than an array.

## Counting records with DB::count()

```php
<?php

$posts = DB::table('posts')->count(); // count all posts
```

This returns and integer of the number of records that match the query.

## Fetching records with DB::join(), DB::leftJoin(), DB::rightJoin(), and DB::select()

```php
<?php

$posts = DB::table('posts')->join('users', 'posts.user_id', '=', 'users.id')->get(); // inner join posts table with users table on posts.user_id = users.id and retrieve all records
$posts = DB::table('posts')->leftJoin('users', 'posts.user_id', '=', 'users.id')->get(); // left join posts table with users table on posts.user_id = users.id and retrieve all records
$posts = DB::table('posts')->rightJoin('users', 'posts.user_id', '=', 'users.id')->get(); // right join posts table with users table on posts.user_id = users.id and retrieve all records
$posts = DB::table('posts')->join('users', 'posts.user_id', '=', 'users.id')->select('posts.*', 'users.id as userId')->get(); // inner join posts table with users table on posts.user_id = users.id and retrieve all records, returning only the id from the users table
```

<aside class="warning">
  SQL indicators such as table and column names can not be bound to prepared statements, so joins and selects are not immune to SQL injection. A simple regex filter is used to deter it, but if dynamic data is used for table or column names it should be manually filtered through an array of whitelisted values first.
</aside>

## Inserting records

```php
<?php

$key = DB::table('posts')->insert(['title' => 'Post Title', 'body' => 'Here goes the post body...']);
$key = DB::table('posts')->insert([
    ['title' => 'Post Title', 'body' => 'Here goes the post body...'],
    ['title' => 'Post Title #2', 'body' => 'Here goes the second post body...']
]);
```

If at least one record is successfully inserted into the database, `DB::insert()` returns the primary key of the last inserted record. If no records are inserted, it returns false.

## Updating records

```php
<?php

$result = DB::table('posts')->update(['title' => 'Post Title']); // update all posts
$result = DB::table('posts')->where('id', 10)->update(['title' => 'Post Title']); // update only posts where id = 10
```

Returns the number of records effected by the query.

## Deleting records

```php
<?php

$result = DB::table('posts')->delete(); // delete all posts
$result = DB::table('posts')->where('id', 10)->delete(); // delete posts where id = 10
```

Returns the number of records effected by the query.

## Direct queries with DB::query()

This method accepts an SQL query string and an optional array of parameters to bind before executing.

```php
<?php

$posts = DB::query('SELECT * FROM posts WHERE user_id = ?', [10]);
$posts = DB::query('SELECT * FROM posts WHERE user_id = :user_id', [':user_id' => 10]);
```
