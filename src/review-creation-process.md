# Review Creation Process    
Review Creation Process Documentation   
## 2. Review Creation Process   
The review creation process is handled by the `ReviewViewSet` and `ReviewService`. Here's an overview of the process:   
### 2.1 Permissions and Eligibility   
Before a user can create a review, the system checks for the following conditions:   
1. The user must be authenticated.   
2. The villa must be in an approved status.   
3. The user must have a past booking for the villa that has ended and was paid for.   
4. The user must not have already reviewed this villa.   
   
These checks are performed by the `ReviewService.can\_review\_villa(user, villa)` method.   
### 2.2 Creating a Review   
1. The client sends a POST request to `/api/villas/{villa\_slug}/reviews/` with review details.   
2. The `ReviewViewSet` handles the request:   
    - It first gets the villa object based on the provided slug.   
    - It then calls `ReviewService.can\_review\_villa(request.user, villa)` to check eligibility.   
    - If eligible, it uses the `ReviewSerializer` to validate the input data.   
    - If validation passes, it calls `ReviewService.create\_or\_update\_review(request.user, villa, serializer.validated\_data)`.   
3. The `ReviewService.create\_or\_update\_review` method:   
    - Creates a new review if one doesn't exist for this user and villa.   
    - Updates the existing review if the user has already reviewed this villa.   
   
### 2.3 Review Serializer   
The `ReviewSerializer` handles the serialization and deserialization of Review objects:   
- It includes fields for `id`, `villa`, `user`, `text`, `rating`, `created\_at`, and `updated\_at`.   
- The `user` field is read-only and automatically set to the current user.   
- The `villa` field is also read-only and set based on the URL.   
- It overrides the `create` and `update` methods to handle the creation and updating of reviews.   
   
## 3. Retrieving Reviews   
The `ReviewViewSet` provides several methods for retrieving reviews:   
1. `list`: Retrieves all reviews for a specific villa.   
2. `retrieve`: Retrieves a specific review by ID.   
3. `my\_reviews`: Retrieves all reviews written by the current user.   
4. `villa\_reviews`: Retrieves all reviews for a specific villa.   
   
These methods use the `ReviewService` to fetch the appropriate reviews from the database.   
## 4. Updating Reviews   
Users can update their existing reviews:   
1. The client sends a PUT or PATCH request to `/api/villas/{villa\_slug}/reviews/{review\_id}/`.   
2. The `ReviewViewSet` handles the request:   
    - It checks if the user has permission to update the review (must be the original author).   
    - It uses the `ReviewSerializer` to validate the updated data.   
    - If validation passes, it updates the review in the database.   
   
## 5. API Endpoints   
1. `POST /api/villas/{villa\_slug}/reviews/`: Create a new review   
2. `GET /api/villas/{villa\_slug}/reviews/`: List all reviews for a villa   
3. `GET /api/villas/{villa\_slug}/reviews/{review\_id}/`: Retrieve a specific review   
4. `PUT /api/villas/{villa\_slug}/reviews/{review\_id}/`: Update a review   
5. `PATCH /api/villas/{villa\_slug}/reviews/{review\_id}/`: Partially update a review   
6. `GET /api/villas/{villa\_slug}/reviews/my\_reviews/`: Retrieve the current user's reviews   
7. `GET /api/villas/{villa\_slug}/reviews/villa\_reviews/`: Retrieve all reviews for a villa   
   
Note: Access to these endpoints is controlled by permissions, ensuring users can only create, view, and modify reviews according to the business rules.   
## 6. Error Handling   
The review creation process includes several error checks:   
1. If the villa is not approved for reviews, a ValidationError is raised.   
2. If the user hasn't stayed at the villa, a ValidationError is raised.   
3. If the user has already reviewed the villa, a ValidationError is raised when trying to create a new review (but updating is allowed).   
   
These errors are caught and returned as appropriate HTTP responses by the `ReviewViewSet`.   
