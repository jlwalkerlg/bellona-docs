# Views

Views are files which hold your presentational markup.

## Creating views

To create a view, simply write a new file in the `app/views` directory.

## Using views

```php
<?php

use Bellona\Support\Facades\View;

$view = app('Bellona\View\ViewFactory')->make('home');
$view->render();

// Alternatively...
View::make('home');
```

> Pass data to a view:

```php
<?php

$view = app('Bellona\View\ViewFactory')->make('home');
$view->render(['title' => 'Welcome!']);

// This will make a $title variable available in app/views/home.php, which holds the value 'Welcome!'.
```

To get access to the view as an instance of `Bellona\View\View`, use the `Bellona\View\ViewFactory::make()` instance method, which takes in the path to your view file, relative to the `app/views` directory and excluding the `.php` extension, as an argument. You can use the `Bellona\Support\Facades\View` facade to make this cleaner.

You can then render your view, thereby sending it as HTML output to the user's browser, by using the `Bellona\View\View::render()` method. This takes in an optional array of data in the form of key-value pairs to pass to the view, so that each key is available as a variable within your view file.

Alternatively, you can use the global `render()` helper, which accepts the path to your view as the first argument, and the optional data array as the second argument.

## Sharing data between views

```php
<?php

use Bellona\Support\Facades\View;

// Share the $title variable between all views hereafter.
View::share(['title' => 'Welcome!']);

// Render the app/views/home.php view, which will have access to the $title variable.
render('home');

// Render the app/views/about.php view, and override the $title variable for this view only.
render('about', ['title' => 'About Us']);
```

To share data between views, use the `Bellona\View\ViewFactory::share()` method, which takes in the data you wish to share as an argument. All view instanced created after this call will then have access to this shared data, in addition to any specific data passed only to a single view.

## Extending templates

> Define an `app/views/templates/default.php` template:

```php
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title><?= (isset($title) ? h($title) . ' | ' : '') . SITE_NAME ?></title>
</head>

<body>

  <!-- Main content -->
  <?php $this->yield('body') ?>

  <!-- Footer -->
  <?php $this->yield('foot') ?>

</body>

</html>
```

> Extend your template from a view and inject content into the default body yield:

```php
<?php $this->extends('default') ?>

<p>This content will be injected into the 'body' yield.</p>
```

> Inject content into the body and the footer yields:

```php
<?php $this->extends('default') ?>

<?php $this->block('body') ?>
  <p>This content will be injected into the 'body' yield.</p>
<?php $this->endblock() ?>

<?php $this->block('foot') ?>
  <p> This content will be injected into the 'foot' yield.</p>
<?php $this->endblock() ?>
```

Bellona allows you use files containing markup as templates which can be extended by your views.

First, write a template file in the `app/views/layouts` directory. This file should call the `Bellona\View\View::yield()` instance method anywhere you wish to inject content from your views which extend this template. The `Bellona\View\View::yield()` takes in the name of your content as an argument. If the content does not exist, an empty string will be injected instead.

Next, write a view file which extends the template using the `Bellona\View\View::extends()` method, which takes in the path to the template to extend, relative to the `app/views/layouts` directory and excluding the `.php` extension, as an argument.

You can define the content you wish to inject within your views by preceding your content with a `Bellona\View\View::block()` call, and ending it with a `Bellona\View\View::endblock()` call. `Bellona\View\View::block()` takes in the name to assign to your content, which is then used by the `Bellona\View\View::yield()` method in your template.

If you do not specify any content blocks, your view will be automatically injected into the 'body' yield, if your template defines one. If you do not specify any content blocks and your template does not define a 'body' yield, nothing will be output. If you do define a content block, the 'body' yield will not be injected automatically, but should be injected using a content block as usual.
