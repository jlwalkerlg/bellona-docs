# Form Validation

The `Bellona\Validation\Validator` library can be used to validate incoming requests.

## Manually validating data

```php
<?php

$data = ['email' => 'invalidemail'];

$rules = ['email' => 'required|format:email'];

$validator = new Bellona\Validation\Validator($data, $rules);

$validator->run();

if ($validator->validates()) {
  // All validations passed...
} else {
  // Get the validation errors.
  $errors = $validator->getErrors();
}
```

To validate data manually, you must instantiate the `Bellona\Validation\Validator` class and pass in any data you wish to validate yourself.

`Bellona\Validation\Validator::__construct()` takes in the data you wish to validate as the first argument, as well as an array of validation rules to run against said data as the second argument. You can then run the validations with the `Bellona\Validation\Validator::run()` method and subsequently check that all validations passed with the `Bellona\Validation\Validator::validates()` method. If all validations passed, this will return `true`; otherwise, it will return false and the validation errors will be captured and retrievable with the `Bellona\Validation\Validator::getErrors()` method.

## Validating request data

```php
<?php

Router::post('/posts', function(Bellona\Http\Request $request) {
  $request->validate([
    'email' => 'required|format:email'
  ]);

  // All validations passed...
});
```

The `Bellona\Http\Request` instance comes with a `Bellona\Http\Request::validate()` function to automatically validate the incoming request data for you. This only takes in the validation rules as an argument, and runs them against the incoming request body automatically.

If any validations fail, they will be automatically flashed to the session as an `errors` array and available from your view as an `$errors` variable. The user will also be redirected back to the previous location and the script will be exited.
