# CSRF Protection

Bellona comes with the CSRF middleware enabled by default. All incoming requests that are not `GET` requests are required to present a CSRF token either in the request body or in an `X-CSRF-TOKEN` header. If a valid token is not presented with the request, the middleware will flash a session message and redirect back to the previous page before exiting the script, though you can change this behaviour by modifying the `App\Middleware\CSRF::deny()` method.

## CSRF tokens in views

```php
<form action="/posts" method="POST">
  <?= CSRF::input() ?>
  ...
</form>
```

To include a valid CSRF in your view, thereby ensuring it gets sent with a form, Bellona provides a `Bellona\Security\CSRF::token()` instance method which returns the valid token as a string. In your view, you should add this as the value of a hidden input field in your form with the name of `csrf_token`. Alternatively, this can be done automatically by using the `Bellona\Security\CSRF::input()` instance method directly in your form.

<aside class="notice">
  Bellona provides a convenient <code>Bellona\Support\Facades\CSRF</code> facade for use within your views so that you do not have to manually resolve the <code>Bellona\Security\CSRF</code> service. This facade is also aliased as <code>CSRF</code> so that you do not have to use the full namespace within your views.
</aside>

## CSRF tokens with ajax

> Output the CSRF token in your view, somewhere it can be retrieved client-side with JavaScript:

```php
  <meta name="csrf-token" content="<?= CSRF::token() ?>"
```

> Send the CSRF token with the request, either in the request body or as an `X-CSRF-TOKEN` header, and capture the new CSRF from the response for use in further requests:

```javascript
fetch("/api/posts", {
  method: "POST",
  headers: {
    "Content-type": "application/json",
    "X-CSRF-TOKEN": document.querySelector('meta[name="csrf-token"]').getAttribute('content');
  },
  body: JSON.stringify({
    title: "The post title",
    body: "The post body"
  })
}).then(res => {
  const newCsrfToken = res.headers.get("XSRF-TOKEN");
  document.querySelector('meta[name="csrf-token"]').setAttribute('content', newCsrfToken);
  ...
});
```

Bellona sends a new CSRF token back with each response in the form of a `XSRF-TOKEN` response header. If you are using ajax to send requests, you should capture this token and return it with any subsequent request that is not a `GET` request, otherwise the request will be denied.
