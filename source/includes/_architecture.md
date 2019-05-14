# Architecture

## The Request Lifecycle

Each and every incoming request which hits your app is directed to the `public/index.php` file by way of `.htaccess` files.

This file first loads the vendor autoloader, then your environment variables, and then your main configuration settings from your `config/index.php` file. Next, a number of convenient class aliases are setup for use within your views, before the app itself is booted. An array of `Service Provider` class names specifies which service providers your application should have access to, and are consequently loaded by the `Service Container`. At this point, the app has been "booted".

Next, the request itself is handled by the `Bellona\Http\Router`, which loads your routes and finds the one which matches the incoming request, runs any middleware specified for that route, and finally runs the callback specified for your route. The `Bellona\Http\Router` also takes care of resolving any services that you have type-hinted in your route middleware or callback from the container and injects them automatically for you.

## The Service Container

All of Bellona's core services are registered with the `Service Container`, which is really just the `Bellona\Core\Application` itself. Many of these services are deferred, meaning they are registered but not loaded immediately; rather, they are only loaded when required.

You are encouraged to register your own services with the container by writing your own `Service Provider` or using the `App\Providers\AppServiceProvider` which comes with Bellona out-of-the-box.

<aside class="notice">
  Services are just classes which provide some key functionality to your application. For example, Bellona provides an <code>Encryptor</code> class for providing an encryption service, a <code>Cookie</code> class for providing a service to easily get/set cookies, etc.
</aside>

Registering services with the container, as opposed to using classes in the usual way, provides a number of benefits:

- You can configure service providers such that the dependencies of your services are automatically taken care of by the service container.
- You can easily register singletons with the container so that the same instance is supplied each time you resolve a service from the container.
- Your services can be automatically injected into your controller methods as well as your middleware (dependency injection).

### Registering your own services.

> An example of registering a (deferred) service that can be used to purify dynamic HTML before outputting it:

```php
<?php

namespace App\Providers;

use Bellona\Support\ServiceProvider;

class PurifierServiceProvider extends ServiceProvider
{
    public $defer = true;

    public $services = ['purifier'];

    public function register()
    {
        $this->app->singleton('purifier', function($app) {
            $config = \HTMLPurifier_Config::createDefault();
            return new \HTMLPurifier($config);
        });
    }
}
```

To register your own service with the container, you would either create your own `Service Provider` or use the `App\Providers\AppServiceProvider` which comes with Bellona out-of-the-box.

A `Service Provider` is a class which extends `Bellona\Support\ServiceProvider` and specifies a `Bellona\Support\ServiceProvider::register()` method. This is called automatically by the application when it boots and should register services by way of either the `Bellona\Core\Application::bind()` method or the `Bellona\Core\Application::singleton()` method.

`Bellona\Core\Application::bind()` will ensure that a new instance of your service is created each time you resolve it from the container, whereas `Bellona\Core\Application::singleton()` will ensure that the same instance is returned each time. You can access the `Bellona\Core\Application` instance as an inherited property from the `Bellona\Support\ServiceProvider`.

Both the `Bellona\Core\Application::bind()` and the `Bellona\Core\Application::singleton()` methods accept the name of the service you wish to register as the first argument, and a callback function as the second argument. The service container is automatically passed to this callback function so you can resolve any other services on which your own service is dependent, and should return an instance of your service.

If you wish to defer a service, so that it only loads when you require it, simply set a public `$defer` boolean property and set it as `true`, as well as a public `$services` array property which specifies the name of the services your provider provides.

After setting up your service provider (and the service itself), you should list the fully-namespaced name of the service provider class in the `$serviceProviders` array, within the `public/index.php` file. This will ensure that the service container knows about your service provider and so can be sure to load it when the application boots on each request.

<aside class="notice">
  You should make sure that no two of your services are dependent on each other for their <code>__construct</code> method, otherwise the container won't be able to resolve them since they each need to be resolved before the other dependent service can be resolved, causing an infinite recursion error. Note that this only applies in the construction of a service; any services can be safely resolved within other methods.
</aside>

### Resolving services from the container

```php
<?php

// Get the container instance.
$app = Bellona\Core\Application::getInstance();

// Resolve the 'purifier' service.
$purifier = $app->resolve('purifier');

// Alternatively:
$purifier = $app['purifier'];

// Or:
$purifier = app('purifier');
```

In order to resolve a service from the container, you will first need a reference to the container instance using `$app = Bellona\Core\Application::getInstance()`. Then you can either call the `$app->resolve($serviceName)` instance method, specifying the name of the service you wish to resolve as an argument, or use array notation like so: `$app[$serviceName]`.

Alternatively, Bellona provides a convenience function to more easily resolve services: `app($serviceName)`.

## Facades

> Below is the source code for the `Bellona\Support\Facades\Encrypt` facade.

```php
<?php

namespace Bellona\Support\Facades;

use Bellona\Encryption\Encryptor;

class Encrypt extends Facade
{
  protected static $service = Encryptor::class;
}

```

> This allows you to use instance methods of the `Bellona\Encryption\Encryptor` class as static methods of the corresponding facade:

```php
<?php

$encrypted = Bellona\Support\Facades\Encrypt::encryptString('secretmessage');

```

> This provides a much more readable syntax to the following, which is equivalent:

```php
<?php

$encrypted = app('Bellona\Encryption\Encryptor')->encryptString('secretmessage');

```

Facades are simply "proxy" classes which defer your static method calls to instance methods of a service registered with the container.

To create a `Facade`, simply extend the `Bellona\Support\Facade` class and define a public static property `$service` whose value is the name of the service you wish to proxy. Then, all static calls to this facade will be automatically redirected to your the corresponding instance methods on service using the dynamic `Bellona\Support\Facade::__callStatic()` function.
