# Reviews API Reference

## Base URL

`/api/villas/{villa_slug}/reviews/`

## Endpoints

### 1. List Reviews for a Villa

**Endpoint:** GET `/api/villas/{villa_slug}/reviews/`  
**Description:** Retrieves a list of all reviews for a specific villa.  
**Parameters:**

- `villa_slug` (path parameter): The slug of the villa to get reviews for.  


**Response:**

```
[
  {
    "id": 1,
    "user": "user@example.com",
    "text": "Great stay!",
    "rating": 5,
    "created_at": "2023-09-19T10:00:00Z",
    "updated_at": "2023-09-19T10:00:00Z"
  },
  // ... more reviews
]

```

**Permissions:** AllowAny (public access)

### 2. Create a Review

**Endpoint:** POST `/api/villas/{villa_slug}/reviews/`  
**Description:** Creates a new review for a specific villa.  
**Parameters:**

- `villa_slug` (path parameter): The slug of the villa to review.  


**Request Body:**

```
{
  "text": "Amazing experience!",
  "rating": 5
}

```

**Response:**

```
{
  "id": 2,
  "user": "user@example.com",
  "text": "Amazing experience!",
  "rating": 5,
  "created_at": "2023-09-19T11:00:00Z",
  "updated_at": "2023-09-19T11:00:00Z"
}

```

**Permissions:** IsAuthenticated  
**Notes:**

- User must have a confirmed booking and have stayed at the villa to leave a review.
- Users can only leave one review per villa.  


### 3. Retrieve a Specific Review

**Endpoint:** GET `/api/villas/{villa_slug}/reviews/{review_id}/`  
**Description:** Retrieves details of a specific review.  
**Parameters:**

- `villa_slug` (path parameter): The slug of the villa.
- `review_id` (path parameter): The ID of the review to retrieve.  


**Response:**

```
{
  "id": 1,
  "user": "user@example.com",
  "text": "Great stay!",
  "rating": 5,
  "created_at": "2023-09-19T10:00:00Z",
  "updated_at": "2023-09-19T10:00:00Z"
}

```

**Permissions:** AllowAny (public access)

### 4. Update a Review

**Endpoint:** PUT/PATCH `/api/villas/{villa_slug}/reviews/{review_id}/`  
**Description:** Updates an existing review.  
**Parameters:**

- `villa_slug` (path parameter): The slug of the villa.
- `review_id` (path parameter): The ID of the review to update.  


**Request Body:**

```
{
  "text": "Updated review text",
  "rating": 4
}

```

**Response:**

```
{
  "id": 1,
  "user": "user@example.com",
  "text": "Updated review text",
  "rating": 4,
  "created_at": "2023-09-19T10:00:00Z",
  "updated_at": "2023-09-19T12:00:00Z"
}

```

**Permissions:** IsAuthenticated
**Notes:** Users can only update their own reviews.

### 5. Delete a Review

**Endpoint:** DELETE `/api/villas/{villa_slug}/reviews/{review_id}/`  
**Description:** Deletes a specific review.  
**Parameters:**

- `villa_slug` (path parameter): The slug of the villa.
- `review_id` (path parameter): The ID of the review to delete.  


**Response:** 204 No Content  
**Permissions:** IsAuthenticated
**Notes:** Users can only delete their own reviews.

### 6. Get User's Reviews

**Endpoint:** GET `/api/villas/{villa_slug}/reviews/my_reviews/`  
**Description:** Retrieves all reviews written by the authenticated user.  
**Response:**

```
[
  {
    "id": 1,
    "villa": "Villa Name",
    "text": "Great stay!",
    "rating": 5,
    "created_at": "2023-09-19T10:00:00Z",
    "updated_at": "2023-09-19T10:00:00Z"
  },
  // ... more reviews
]

```

**Permissions:** IsAuthenticated

## Error Responses

- 400 Bad Request: Invalid data provided
- 401 Unauthorized: Authentication credentials were not provided
- 403 Forbidden: User doesn't have permission to perform the action
- 404 Not Found: The requested review or villa does not exist  


## Notes

- The rating must be between 0 and 10.
- Review creation is restricted to users who have stayed at the villa.
- Users can only leave one review per villa, but they can update their existing review.
- All times are returned in ISO 8601 format.
