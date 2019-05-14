# File Uploads

The `Bellona\Uploads\FileUpload` class can be used to securely verify and store files uploaded by the user in the request.

## Uploading files

```php
<?php

$file = $_FILES['image'];

$upload = new Bellona\Uploads\FileUpload($file);

if ($upload->store(PUBLIC_ROOT . '/uploads') {
  // File was successfully uploaded...
  $uploadedName = $upload->getName();
} else {
  // Get the error which caused the failure.
  $error = $upload->getError();
}
```

`Bellona\Uploads\FileUpload::__construct()` takes the specific array within the `$_FILES` superglobal corresponding to the file you wish to upload as an argument. You can then store the file permanently with the `Bellona\Uploads\FileUpload::store()` method, which takes in the absolute path to the directory where you want to store the file as an argument. If the file was successfully stored, this will return `true`; otherwise it will return false and the error which caused the failure can be retrieved with the `Bellona\Uploads\FileUpload::getError()` method.

The `Bellona\Uploads\FileUpload` class has some default behaviours you should be aware of.

- The maximum size allowed for a file is the same as `upload_max_filesize` in your `php.ini` file.
- If the destination directory does not exist, it will be created.
- The file will be renamed by appending a number to the end of the file name if a file with the same name already exists in the destination directory.
- Only the following extensions are permitted: jpg, jpeg, gif, png, svg, webp.
- Any whitespace in the file name will be replaced with underscores.

You can override most of these defaults as well as setting other options with the `Bellona\Uploads\FileUpload::setOptions()` array, which takes in an array of options you wish to set.

If you wish to retrieve the name with which a file was uplaoded, you can use the `Bellona\Uploads\FileUpload::getName()` instance method.

## Deleting a file

```php
<?php

$upload->delete();
```

If you wish to delete a file after storing it, for example if something goes wrong later in your script, you can use the `Bellona\Uploads\FileUpload::delete()` method, which returns `true` if the file was successfully delete; otherwise it will return false.

## Uploading multiple files

```php
<?php

$profilePic = $_FILES['profile_pic'];
$coverImg = $_FILES['cover_img'];

$files = ['profilePic' => $profilePic, 'coverImg' => $coverImg]
$destination = PUBLIC_ROOT . '/uploads';

$result = Bellona\Uploads\FileUpload::upload($files, $destination);

if ($result) {
  // All files were successfully uploaded...
  $profilePicName = Bellona\Uploads\FileUpload::getUploadedName('profilePic');
  $coverImgName = Bellona\Uploads\FileUpload::getUploadedName('coverImg');
} else {
  // Something went wrong...
}
```

The `Bellona\Uploads\FileUpload` class provides a `Bellona\Uploads\FileUpload::upload()` static method for uploading multiple files at once. It takes an array of files to upload as the first argument and a destination directory as a second argument. Optionally, you can also pass a third argument specifying the options to set for all files. It will return true on success and false on failure.

You can then get the name with which any of the files were uploaded using the `Bellona\Uploads\FileUpload::getUploadedName()` static method and passing it the key corresponding to the file whose name youi wish to retrieve.

If something goes wrong while uploading one of the files, all the files that were already uploaded will be deleted automatically.
