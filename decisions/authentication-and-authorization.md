# Authentication and Authorization

There are many different ways to implement authentication and authorization in a web application.
This document summarizes and encompases findings on this subject and help explain the path that was taken.

This document structure is as follows

1. [Authentication vs Authorization](#Authentication-vs-Authorization)
2. [JSON Web Tokens](#JWT)
   1. [Benefits](#Benefits)
   2. [Usage](#Usage)
   3. [Implementation](#Implementation)
3. [Oauth2](#Oauth2)
   1. [Web Server Applications](#Web-Server-Applications)
   2. [Providers](#Providers)
   3. [Python Libraries](#Python-Libraries)
4. [Future Direction](#Future-Direction)

## Authentication vs Authorization

The Cultural Awareness application requires both Authentication and Authorization when it comes to creating Administrators and
making sure they have the right permission level in order to perform operations on the database. Without proper implementation of both authentication and authorization it opens up the application for criticism of validity.

### Authentication

Authentication is the action of validating if one is who they say they are

[Merriam-Webster](https://www.merriam-webster.com/dictionary/authentication)

### Authorization

Authorization is the action of validation if one has the permissions to perform a task

[Merriam-Webster](https://www.merriam-webster.com/dictionary/authorization)

## JWT

JSON Web Tokens are a way to securely authenticate and (possibly) authorize a user.

```json
{
  "admin": true,
  "email": "test@example.com",
  "exp": "<expiration-date>"
}
```

### Benefits

- A [Standard](https://tools.ietf.org/html/rfc7519)
- Easy to implement -- Many Python libraries implement JWTs
- Token is used to authenticate/authorize subsequent requests rather than always having to login with email and password
- Prevents a stale JWT from permitting malicious operations

### Usage

1. User sends their username and password
   This is done by providing `email` and `password` in the `/login` endpoint.
   Following [Authentication Header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

   ```text
   Authentication: Basic <email>:<password>
   ```

   **NOTE:** The email or username **MUST** not include a colon as this is used to split the email and password. The email and password are
   both [Base64](https://en.wikipedia.org/wiki/Base64) encoded.

2. User is authenticated
   The password is checked to see if it matches the encrypted password stored in the database

3. Server returns a JWT

   ```json
   {
     "admin": true,
     "email": "test@example.com",
     "exp": "1 day after today"
   }
   ```

4. User provides the JWT in all future requests that require a permission level
   This is done in the Header of the Request.

   ```text
   Authorization: Bearer <token>
   ```

5. Token is decrypted by the server and identifies the user and authenticates them

   It also verifies that the Token isn't expired by checking to see if the `exp` field is **AFTER** the current date.

### Python Libraries

- [pyjwt](https://github.com/jpadilla/pyjwt/)
- [python-jose](https://github.com/mpdavis/python-jose/)
- [authlib](https://github.com/lepture/authlib)

## Oauth2

Oauth2 allows users to login to their favorite platform and enables the Cultural Awareness application to view and use their information from that platform
on our platform. This makes life easier for users only having to remember passwords for a given platform rather than an additional platform. They also don't have to add information to yet another account. Oauth2 also has many different workflows for different types of applications. This application will focus exclusively on Authorization Code.

### Web Server Applications

- [x] Needs a Frontend
- [x] Needs a Backend
- [x] has User Interaction
- [x] Needs Client secret

The steps for this method are best depcited by an image.

![Oauth2  Server application drawing](../images/OAuth2-Web-Server-Application.png)

Explanation for [Google OAuth2](https://developers.google.com/identity/protocols/oauth2/web-server).

### Providers

- [Google](https://developers.google.com/identity/protocols/oauth2)
- Others...

### Python Libraries

- [google-api-python-client](https://github.com/googleapis/google-api-python-client)
- [rauth](https://github.com/litl/rauth)
- [oauthlib](https://github.com/oauthlib/oauthlib)
- [flask-dance](https://github.com/singingwolfboy/flask-dance)

## Future Direction

1. OSUMC-Cultural-Awareness specific Users (JWT)
   1. Allow Users to create a user and all credentials stored on our servers
   2. Encrypt Password on server in order to securely store important information
   3. JWT returned to user and used to authenticate and authorize a user
2. OAuth2 is a niceity that can be implemented later
   1. Nice for users
   2. More Modern
   3. Not directly important for the application
