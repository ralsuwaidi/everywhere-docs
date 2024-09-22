# User Authentication    
User Authentication Documentation   
## Overview   
The Everywhere system uses token-based authentication. Users obtain a token by providing their credentials, and then use this token for subsequent authenticated requests.   
## Authentication Process   
1. The user sends their credentials (email and password) to the token endpoint.   
2. If the credentials are valid, the server generates and returns an authentication token.   
3. For subsequent requests, the client includes this token in the Authorization header.   
   
## Token Endpoint   
- **URL**: `/api/auth-token/`   
- **Method**: POST   
- **Authentication**: Not required   
   
## Obtaining a Token   
### Request   
- **URL**: `/api/auth-token/`   
- **Method**: POST   
- **Content-Type**: application/json   
   
Payload:   
```
{
    "username": "user@example.com",
    "password": "userpassword"
}

```
Note: Even though the system uses email for authentication, the payload key is "username" due to DRF's default `obtain\_auth\_token` view.   
### Response   
### Success Response   
- **Code**: 200 OK   
- **Content**: JSON object containing the auth token   
   
Example:   
```
{
    "token": "9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b"
}

```
### Error Response   
- **Code**: 400 BAD REQUEST   
- **Content**: JSON object with error details   
   
Example:   
```
{
    "non_field_errors": [
        "Unable to log in with provided credentials."
    ]
}

```
## Using the Token   
After obtaining the token, include it in the `Authorization` header of subsequent requests:   
```
Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b

```
## Implementation Details   
The authentication system uses Django Rest Framework's built-in TokenAuthentication.   
1. The token endpoint is provided by DRF's `obtain\_auth\_token` view:   
    ```
from rest_framework.authtoken.views import obtain_auth_token

urlpatterns = [
    # ... other URL patterns ...
    path("api/auth-token/", obtain_auth_token),
]

```
2. This view is linked to the `AuthToken` model, which stores the tokens associated with users.   
3. When a valid request is made to this endpoint, it either creates a new token or returns the existing token for the user.   
   
## Configuration   
Ensure that `rest\_framework.authtoken` is included in your `INSTALLED\_APPS` in `settings.py`:   
```
INSTALLED_APPS = [
    # ... other apps ...
    'rest_framework.authtoken',
]

```
Also, configure DRF to use token authentication:   
```
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],
}

```
## Token Lifespan   
By default, tokens do not expire. If you want tokens to expire, you'll need to implement custom token management or use a third-party package like `django-rest-knox` for more advanced token features.   
## Notes   
- The system uses email for user identification, but the token request still uses the key "username" for the email field. (todo)   
