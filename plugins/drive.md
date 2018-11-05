---
layout: default
title: Drive
title_nav: Drive
description: Cloud-based file and image management for TinyMCE.
keywords: tinydrive storage media tiny drive
---

The Tiny Drive plugin adds the functionality to upload and manage files and images to the cloud. This plugin is only available in [Tiny Cloud](https://www.tiny.cloud/download/) and requires you to register for an API key.

To enable this functionality, add `tinydrive` to the list of plugins in the `tinymce.init` call. You also need to authenticate the user using a [JSON Web Token]({{site.baseurl}}/configure/jwt-authentication) (JWT).

Once you enable Drive it integrates as the default file picker for the Image, Link, and Media dialogs and as the default upload handler for local images being pasted or inserted into the document.


### Example

```js
tinymce.init({
  selector: 'textarea',  // change this value according to your html
  plugins: 'tinydrive'
});
```

## Options

These settings are necessary to make Drive work:

### `tinydrive_token_provider`

This setting could take one of the following two forms:

* A URL to a page that takes an HTTP JSON POST request and produces a JSON structure with a valid JWT. It uses a POST request to avoid caching by browsers and proxies.
* A function that provides the same token through a callback. This allows you to make your own HTTP request in any format you like. The provider function is a function that has a success and failure callback where the success takes an object with a token property containing the JWT, and the failure callback takes a string to present as an error message if the token could not be produced.

You can read more about how to create these tokens in the [JWT authentication]({{site.baseurl}}/configure/jwt-authentication/) guide.

**Type:** `String` or `Function`

**Required:** yes

#### Example Using a JWT Provider URL

```js
tinymce.init({
  selector: 'textarea',  // change this value according to your HTML
  plugins: 'tinydrive',
  tinydrive_token_provider: '/jwt'
});
```

#### Example Using a JWT Provider Callback

```js
tinymce.init({
  selector: "textarea",  // change this value according to your HTML
  plugins: "tinydrive",
  tinydrive_token_provider: function (success, failure) {
     success({ token: 'jwt-token' });
     // failure('Could not create a jwt token')
  }
});
```

### `tinydrive_upload_path`

This setting enables you to change the default upload path for files that get uploaded when pasted into the editor, uploaded directly through the Image dialog, or when you drag-and-drop images into the editor. It will produce a date-based structure within this path like this `/uploads/{year}{month}{day}`. This is to avoid having thousands of files in the same directory.

**Type:** `String`

**Default Value:** `"/uploads"`

#### Example

```js
tinymce.init({
  selector: "textarea",  // change this value according to your HTML
  plugins: "tinydrive",
  tinydrive_upload_path: '/some/other/path'
});
```

## Insert File toolbar button

Drive will automatically integrate into the Image, Link, and Media dialogs as a file picker. You can also configure it to insert files directly into your content using the `insertfile` button. To enable this button, add it to your toolbar editor setting.

The Insert File toolbar button will insert images as `img` elements or other files as links to that file.

### Example of toolbar button

```js
tinymce.init({
  selector: 'textarea',  // change this value according to your HTML
  plugins: 'tinydrive',
  toolbar: 'insertfile'
});
```

## Insert File menu item

Drive will automatically integrate into the Image, Link, and Media dialogs as a file picker. You can also configure it to insert files directly into your content using the `insertfile` menu item. To enable this menu item, add it to your menus editor setting or the insert_button_items setting.

The Insert File menu item will insert images as `img` elements or other files as links to that file.

### Example of menu item

```js
tinymce.init({
  selector: 'textarea',  // change this value according to your HTML
  plugins: 'tinydrive',
  toolbar: 'insert',
  menu: {
    insert: { title: 'Insert', items: 'insertfile' }
  },
  insert_button_items: 'insertfile'
});
```

## Restrictions and Quotas

Drive has restrictions on what files can be uploaded and how large these files can be:

* The maximum file size is 100MB
* Allowed image extensions: gif, jpeg, jpg, png, tif, tiff, bmp
* Allowed document extensions: doc, xls, ppt, pps, docx, xlsx, pptx, pdf, rtf, txt, keynote, pages, numbers
* Allowed audio extensions: wav, wave, mp3, ogg, ogv, oga, ogx, ogm, spx, opus
* Allowed video extensions: mp4, m4v, ogv, webm, mov
* Allowed archive extensions: zip
* The Copy operation is limited to single files due to technical reasons.

Your storage and bandwidth quota varies based upon the [Tiny Cloud Plan](https://www.tiny.cloud/pricing/) you are subscribed to.

## Upload Files URL

All files are uploaded to a central storage with a CDN endpoint that means that we are hosting your files and they are publicly available in read-only mode for anyone that has access to the URL of that file.
The URL format for each file is `https://drive.tiny.cloud/1/{your-api-key}/{uuid}` and gets generated when a file is uploaded.
If you move or rename a file, it will still have the same unique URL, so the restructuring of your files using Drive won't affect where they are being used. However, deleting a file will mark the URL as being unused, and the URL will not continue to work.

## User specific root

It's common that you want to be able to have user specific paths so that each user within your system gets it's own directory. This can be done by setting the `https://claims.tiny.cloud/drive/root` custom jwt claim to a path within your tiny drive account. This path will automatically be constructured when the user is accessing drive using a jwt key with that claim. The user only be able to see and manage files within that root.