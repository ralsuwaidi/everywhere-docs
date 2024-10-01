# User API Documentation

# User API

## 1. User Registration

### Description

Register a new user in the system.

### Endpoint

`POST /api/users/register/`

### Authentication

No authentication required.

### Request Body

```
{
  "email": "user@example.com",
  "name": "John Doe",
  "password": "securePassword123"
}

```

| Field    | Type   | Required | Description          |
| :------- | :----- | :------- | :------------------- |
| email    | string | Yes      | User's email address |
| name     | string | Yes      | User's full name     |
| password | string | Yes      | User's password      |

### Response

### Success Response

**Code:** 201 CREATED  
**Content example:**

```
{
  "id": 1,
  "email": "user@example.com",
  "name": "John Doe"
}

```

### Error Response

**Code:** 400 BAD REQUEST  
**Content:**

```
{
  "email": ["This email is already registered."],
  "password": ["This password is too common."]
}

```

### Notes

- Email must be unique in the system.
- Password should meet minimum security requirements.  


### Example Usage

```
curl -X POST -H "Content-Type: application/json" -d '{"email": "user@example.com", "name": "John Doe", "password": "securePassword123"}' http://localhost:8000/api/users/register/

```

## 2. User Login

### Description

Authenticate a user and receive an access token.

### Endpoint

`POST /api/users/login/`

### Authentication

No authentication required.

### Request Body

```
{
  "email": "user@example.com",
  "password": "securePassword123"
}

```

| Field    | Type   | Required | Description          |
| :------- | :----- | :------- | :------------------- |
| email    | string | Yes      | User's email address |
| password | string | Yes      | User's password      |

### Response

### Success Response

**Code:** 200 OK  
**Content example:**

```
{
  "token": "9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b",
  "user_id": 1,
  "email": "user@example.com"
}

```

### Error Response

**Code:** 401 UNAUTHORIZED  
**Content:**

```
{
  "error": "Invalid credentials"
}

```

### Notes

- The token should be included in the Authorization header for authenticated requests.  


### Example Usage

```
curl -X POST -H "Content-Type: application/json" -d '{"email": "user@example.com", "password": "securePassword123"}' http://localhost:8000/api/users/login/

```

## 3. Refresh Token

### Description

Refresh the authentication token.

### Endpoint

`POST /api/users/refresh_token/`

### Authentication

No authentication required, but a valid token must be provided in the request body.

### Request Body

```
{
  "token": "9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b"
}

```

| Field | Type   | Required | Description             |
| :---- | :----- | :------- | :---------------------- |
| token | string | Yes      | The current valid token |

### Response

### Success Response

**Code:** 200 OK  
**Content example:**

```
{
  "token": "8923a09199c62bcf9418ad846dd0e4bbdfc6ee4b",
  "user_id": 1,
  "email": "user@example.com"
}

```

### Error Response

**Code:** 400 BAD REQUEST  
**Content:**

```
{
  "error": "Invalid token"
}

```

### Notes

- This endpoint is used to refresh the token before it expires.
- The new token should be used for subsequent authenticated requests.  


### Example Usage

```
curl -X POST -H "Content-Type: application/json" -d '{"token": "9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b"}' http://localhost:8000/api/users/refresh_token/

```

## 4. Get User Profile

### Description

Retrieve the profile information of the authenticated user.

### Endpoint

`GET /api/users/me/`

### Authentication

Token authentication required.

### Request Parameters

No parameters required.

### Response

### Success Response

**Code:** 200 OK  
**Content example:**

```
{
  "id": 1,
  "email": "user@example.com",
  "name": "John Doe",
  "url": "http://example.com/api/users/1/"
}

```

### Error Response

**Code:** 401 UNAUTHORIZED  
**Content:**

```
{
  "detail": "Authentication credentials were not provided."
}

```

### Notes

- This endpoint returns the profile of the authenticated user.
- The returned fields may vary based on your UserSerializer configuration.  


### Example Usage

```
curl -X GET -H "Authorization: Token fbe3e387d3d449dccd5370c5c428ef6fc13876be" http://localhost:8000/api/users/me/

```
