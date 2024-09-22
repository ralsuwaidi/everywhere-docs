# User API Reference   
## Base URL   
`/api/users/`   
## Endpoints   
### 1. List Users   
- **URL:** `/`   
- **Method:** GET   
- **Auth required:** Yes   
- **Permissions required:** IsAuthenticated   
- **Description:** Retrieves a list of users. Note: This endpoint is restricted to return only the current user's information.   
   
### Response   
```
[
  {
    "id": 1,
    "name": "John Doe",
    "url": "http://example.com/api/users/1/",
    "email": "john@example.com"
  }
]

```
### 2. Retrieve User   
- **URL:** `/<int:pk>/`   
- **Method:** GET   
- **Auth required:** Yes   
- **Permissions required:** IsAuthenticated   
- **Description:** Retrieves details of a specific user. Note: This endpoint is restricted to return only the current user's information.   
   
### Response   
```
{
  "id": 1,
  "name": "John Doe",
  "url": "http://example.com/api/users/1/",
  "email": "john@example.com"
}

```
### 3. Update User   
- **URL:** `/<int:pk>/`   
- **Method:** PUT/PATCH   
- **Auth required:** Yes   
- **Permissions required:** IsAuthenticated   
- **Description:** Updates the details of the current user.   
   
### Request Body   
```
{
  "name": "John Updated Doe",
  "email": "john.updated@example.com"
}

```
### Response   
```
{
  "id": 1,
  "name": "John Updated Doe",
  "url": "http://example.com/api/users/1/",
  "email": "john.updated@example.com"
}

```
### 4. Current User Details   
- **URL:** `/me/`   
- **Method:** GET   
- **Auth required:** Yes   
- **Permissions required:** IsAuthenticated   
- **Description:** Retrieves details of the currently authenticated user.   
   
### Response   
```
{
  "id": 1,
  "name": "John Doe",
  "url": "http://example.com/api/users/1/",
  "email": "john@example.com"
}

```
### 5. User Registration   
- **URL:** `/register/`   
- **Method:** POST   
- **Auth required:** No   
- **Permissions required:** AllowAny   
- **Description:** Registers a new user account.   
   
### Request Body   
```
{
  "email": "newuser@example.com",
  "name": "New User",
  "password": "securepassword123"
}

```
### Response   
```
{
  "email": "newuser@example.com",
  "name": "New User"
}

```
## Notes   
1. The User API is designed with security in mind. Most endpoints require authentication and are restricted to operate only on the current user's data.   
2. The registration endpoint (`/register/`) is the only endpoint that doesn't require authentication, allowing new users to create accounts.   
3. Password fields are write-only and are never returned in responses.   
4. The API uses email as the primary identifier for users, rather than a username.   
5. The `url` field in responses is a hyperlink to the user's detail endpoint, following REST principles.   
   
## Error Responses   
All endpoints may return the following error responses:   
- 400 Bad Request: If the request data is invalid.   
- 401 Unauthorized: If authentication credentials are missing or invalid.   
- 403 Forbidden: If the authenticated user doesn't have permission for the requested action.   
- 404 Not Found: If the requested user does not exist.   
   
Each error response will include a message explaining the error.   
## Rate Limiting   
API endpoints may be subject to rate limiting. Please refer to the API headers for specific limits.   
   
