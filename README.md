# Overview

The Eyefi Cloud API is a RESTful API that uses OAuth 2.0 for authentication.

APIs are provided that allow for photo upload and download, album creation and curation, tag creation and curation, and photo searching. Eyefi will continue to maintain the publicly available API set through regular updates to the cloud-api-doc GitHub project.

Each API endpoint is described in a separate file in the endpoints folder. Parameter descriptions are provided for each API. SDKs are availabe in [JavaScript](https://github.com/eyefi/eyefi-js), [Python](https://github.com/eyefi/cloud-api-python) and [PHP](https://github.com/dayalav/eyefiphp).

Support for the API is provided on a best-effort basis through [Github Issues](https://github.com/eyefi/cloud-api-doc/issues).

# Swagger 

<img src="http://swagger.io/images/swaggerLogo360.png" align="right" height="40" />

[Swagger](http://swagger.io) is a specification and complete framework implementation for describing, producing, consuming, and visualizing RESTful web services. It comes with dozens of resources from SDK generators to API consoles.

The Eyefi Cloud API is Swagger 1.2 compliant. The Swagger UI API console and API documentation can be found at http://swagger.eyefi.com, which is the most up-to-date information available.

# Authentication

Eyefi's Cloud API uses the [OAuth 2.0 authorization framework](http://tools.ietf.org/html/rfc6749) for simple-but-effective authentication and authorization.

All requests must happen over **https**.

## Obtaining an Access Token

An end user will obtain an access token from the [Eyefi Web App](https://app.eyefi.com). Under the account menu tree there is an option named "Developer," which controls the current list of access tokens. Select the **+** icon to generate a new access token to use to contact the API. Use this screen to remove or regenerate access tokens as well.

## Using the Access Token
 
Once the token has been obtained, it should be passed with every API call in an authorization header:

```
Authorization: Bearer ACCESS_TOKEN
```

For example, if you were using curl to get the list of albums in a user's account, it would look like this:

```
curl https://api.eyefi.com/3/albums
    -H 'Authorization: Bearer ACCESS_TOKEN'
    -X 'GET'
```

# API General Use

## Requests

Each of the API endpoints has a series of operations with a mixture of POST, GET, PUT, and DELETE.

For POST and PUT requests, the data can be passed in the body using content 
type `application/x-www-form-urlencoded`:

```
curl https://api.eyefi.com/3/albums
    -H 'Authorization: Bearer ACCESS_TOKEN' 
    -X 'POST' 
    -d 'name=NAME' 
```

or content type `application/json`:

```
curl https://api.eyefi.com/3/albums
    -H 'Authorization: Bearer ACCESS_TOKEN' 
    -X 'POST'
    -H 'Content-Type: application/json' 
    -d '{"name":"NAME"}'
```

### Notes on Documentation

Each API call has a list of required parameters. The first parameters in the list may be required in the URL, especially those that refer to the object ID or the referenced object ID, and additional parameters are in the body.

An example is updating an album (rename album 12345678 to be "NEWNAME"). The documentation for updating an album will refer to the API call as:

```
PUT /albums/{albumId}
```

Which indicates that the `{albumId}` needs to be in the URL. The parameters table looks like this:

<br>

| Name | Type | Description |
|------|:----:|-------------|
| albumId | integer | The ID of the album to update. |
| name | string | The new album name. |
| privacy | integer | Privacy setting for the album. Use 0 for private and 1 for public. Setting privacy to 1 also returns a share URL. |

This table explains the meaning of all parameters, some of which are in the URL and some of which should be form or JSON encoded in the body.

A curl example then looks like this, with the object ID in the URL as `{albumId}` and the `name` parameter in the body:

```
curl https://api.eyefi.com/3/albums/12345678
    -H 'Authorization: Bearer ACCESS_TOKEN' 
    -X 'PUT'
    -H 'Content-Type: application/json' 
    -d '{"name":"NEWNAME"}'
```

## Responses

All responses come in JSON format in either a simple envelope around a dictionary:

```JavaScript
{
    "id": 100000000,
    "name": "NAME",
    "file_count": FILE_COUNT    
}
```

or a list:

```JavaScript
[
    {
        "id": 100000001,
        "name": "NAME",
        "file_count": FILE_COUNT
    },
    {
        "id": 100000002,
        "name": "NAME",
        "file_count": FILE_COUNT
    }
]
```

## Status Codes

| Code | Description |
|--------|-------------|
| 200 | OK. |
| 201 | Resource created. Common for successful POSTs. |
| 204 | No content. Common for successful DELETEs when no data is returned. |
| 400 | Bad request. Common with malformed input such as an invalid email address, bad JSON, etc. |
| 401 | Unauthorized. Invalid or expired access token. |
| 403 | Forbidden. Happens when trying to access a resource for which a user does not have permission. |
| 404 | Not found. |
| 409 | Conflict. Resource already exists, such as an email address already in use. |
| 500 | Internal server error. |

## Errors

When an error occurs the server returns a 4xx or 5xx HTTP status code and an error object with more information:

```JavaScript
{
    "message": "Invalid name",
    "status": 400,
    "code": 2060
}
```

## Miscellaneous Items

### Dates

All date fields are returned as [ISO 8601](https://www.google.com/search?q=%22ISO+8601%22)-formatted strings and are normalized to UTC.

```
2014-02-03T12:37:08+00:00
```

## Endpoint Listing

* [Events](endpoints/events.md)
* [Albums](endpoints/albums.md)
* [Tags](endpoints/tags.md)
* [Files](endpoints/files.md)
* [Search](endpoints/search.md)
