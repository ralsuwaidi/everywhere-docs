# Review Creation Process

Review Creation Process Documentation

## **1. Business Rules for Reviews**

1. Only authenticated users can create, view, or modify reviews.
2. A villa must be in an approved status before it can be reviewed.
3. Users can only review villas they have stayed at (i.e., they must have a past, paid booking for the villa).
4. Users can only submit one review per villa they've stayed at.
5. Users can update their existing reviews.
6. Users cannot review a villa for future bookings.
7. All users (authenticated or not) can view reviews for a villa.
8. Only the original author of a review can modify it.
9. Staff members cannot create or modify reviews on behalf of users.
10. Reviews must include both a text component and a numerical rating.
11. The numerical rating must be between 0 and 10.
12. Reviews are associated with both the user who wrote them and the villa they are about.
13. The system keeps track of when reviews are created and last updated.

## 2. Review Creation Process

The review creation process is handled by the `ReviewViewSet` and `ReviewService`. Here's an overview of the process:

### 2.1 Permissions and Eligibility

Before a user can create a review, the system checks for the following conditions:

1. The user must be authenticated.
2. The villa must be in an approved status.
3. The user must have a past booking for the villa that has ended and was paid for.
4. The user must not have already reviewed this villa.

These checks are performed by the `ReviewService.can_review_villa(user, villa)` method.

### 2.2 Creating a Review

1. The client sends a POST request to `/api/villas/{villa_slug}/reviews/` with review details.
2. The `ReviewViewSet` handles the request:
   - It first gets the villa object based on the provided slug.
   - It then calls `ReviewService.can_review_villa(request.user, villa)` to check eligibility.
   - If eligible, it uses the `ReviewSerializer` to validate the input data.
   - If validation passes, it calls `ReviewService.create_or_update_review(request.user, villa, serializer.validated_data)`.
3. The `ReviewService.create_or_update_review` method:
   - Creates a new review if one doesn't exist for this user and villa.
   - Updates the existing review if the user has already reviewed this villa.

### 2.3 Review Serializer

The `ReviewSerializer` handles the serialization and deserialization of Review objects:

- It includes fields for `id`, `villa`, `user`, `text`, `rating`, `created_at`, and `updated_at`.
- The `user` field is read-only and automatically set to the current user.
- The `villa` field is also read-only and set based on the URL.
- It overrides the `create` and `update` methods to handle the creation and updating of reviews.  


## 3. Retrieving Reviews

The `ReviewViewSet` provides several methods for retrieving reviews:

1. `list`: Retrieves all reviews for a specific villa.
2. `retrieve`: Retrieves a specific review by ID.
3. `my_reviews`: Retrieves all reviews written by the current user.
4. `villa_reviews`: Retrieves all reviews for a specific villa.

These methods use the `ReviewService` to fetch the appropriate reviews from the database.

## 4. Updating Reviews

Users can update their existing reviews:

1. The client sends a PUT or PATCH request to `/api/villas/{villa_slug}/reviews/{review_id}/`.
2. The `ReviewViewSet` handles the request:
   - It checks if the user has permission to update the review (must be the original author).
   - It uses the `ReviewSerializer` to validate the updated data.
   - If validation passes, it updates the review in the database.

## 5. API Endpoints

1. `POST /api/villas/{villa_slug}/reviews/`: Create a new review
2. `GET /api/villas/{villa_slug}/reviews/`: List all reviews for a villa
3. `GET /api/villas/{villa_slug}/reviews/{review_id}/`: Retrieve a specific review
4. `PUT /api/villas/{villa_slug}/reviews/{review_id}/`: Update a review
5. `PATCH /api/villas/{villa_slug}/reviews/{review_id}/`: Partially update a review
6. `GET /api/villas/{villa_slug}/reviews/my_reviews/`: Retrieve the current user's reviews
7. `GET /api/villas/{villa_slug}/reviews/villa_reviews/`: Retrieve all reviews for a villa

Note: Access to these endpoints is controlled by permissions, ensuring users can only create, view, and modify reviews according to the business rules.

## 6. Error Handling

The review creation process includes several error checks:

1. If the villa is not approved for reviews, a ValidationError is raised.
2. If the user hasn't stayed at the villa, a ValidationError is raised.
3. If the user has already reviewed the villa, a ValidationError is raised when trying to create a new review (but updating is allowed).

These errors are caught and returned as appropriate HTTP responses by the `ReviewViewSet`.
