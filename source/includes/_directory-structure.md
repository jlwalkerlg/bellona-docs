# Directory Structure

## app

The `app` directory is where the majority of your development time will be spent, since it contains all of your own controllers, models, views, middleware, libraries, and services.

- `app/controllers` contains all of your controller classes. As with all of your class files, each file should be named the same as the the class it contains, starting with an uppercase letter. E.g. `Home.php` would contain the `Home` controller. You may also nest your controllers into subdirectories within the `app/controllers` directory, so long as your routes reflect this nesting.
- `app/models` contains all of your models. They should be named in the same fashion as your `app/controllers` files and can be similarly nested within subdirectories.
- `app/views` contains all of your views.
- `app/middleware` contains all of your middleware.
- `app/policies` contains all of your authorization policies.
- `app/providers` provides a convenient place to store your service providers. However, you may place your service providers wherever you want, as long as you list them appropriately in the `public/index.php` file.
- `app/facades` contains all of your facades, though you may choose to put these wherever you like.
- `app/lib` contains any libraries you might wish to create, though you are free to create these any place you desire.
- `app/helpers` contains any helper classes/functoins you might wish to create, though you can place these wherever you like.

## config

The `config` contains all of your configuration settings, divided into separate files for each configuration. Note that many of these settings are loaded directly from the `.env` file in your project root directory, so should be changed there.

- `config/index.php` contains the main configuration settings for your site, including file and URL paths.
- `config/session.php` contains configuration settings for user sessions, including which driver you wish to use.
- `config/auth.php` contains configuration settings for authentication, including which driver you wish to use.
- `config/middleware.php` contains an array of middleware you wish make available to your application, where the keys are the names you wish to assign and the values are the fully-namespaced class names of the middleware classes themselves.

If you wish to store your own configuration settings, you are free to do so in the `config/index.php` file, or use this file to include other configuration files you may wish to create.

## public

The `public` directory contains all of the assets that should be available publicly to the request client (i.e. user's browser), including any JavaScript files, CSS, images, and uploads. Other than the `index.php` file and the `.htaccess` file, you are free to arrange these public assets in any way you like.

- `public/index.php` is the main PHP file through which all of the incoming requests to your site are directed, and bootstraps your entire application.
- `public/.htaccess` rewrites the incoming request URL so that all requests, other than those for existing files (e.g. public images), are directed to the `public/index.php` file.

## resources

The `resources` directory is completely optional but provides a convenient place to place any build assets that have to be bundled before being placed into the `public` directory.

## routes

The `routes` directory contains all of the endpoints to which your application can respond.

- `routes/web.php` contains all of your regular endpoints to which your application responds with regular HTML output.
- `routes/api.php` contains all of your API endpoints. These are automatically prefixed with `/api` when they are loaded by the `Bellona/Http/Router`.

## storage

The `storage` directory provides a place where you application will automatically store files such as cached HTML output or encryption keys.

- `storage/cache` contains all your cached HTML output.

## .env

The `.env.example` file in your project root directory contains all of the environment variables which will be loaded by the application. This should be renamed to `.env` and never commited to a public version control repository, so is listed in the `.gitignore` file by default.

## Dockerfile and docker-compose.yml

These are convenient, pre-built files for Docker users, suitable for development.

## .htaccess

The `.htaccess` file in your project root directory directs all incoming requests into the `public` directory (where it is further directed to the `public/index.php` file by the `public/.htaccess` file).

## composer.json

The `composer.json` file specifies all of your project's dependencies, as well as configuring the vendor autoloader.
