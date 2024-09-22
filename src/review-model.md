# Review Model   
Review Model Documentation   
## 1. Review Model   
### 1.1 Fields   
1. `villa` (ForeignKey to Villa)   
    - Description: The villa being reviewed   
    - Related Name: "reviews"   
    - On Delete: CASCADE   
2. `user` (ForeignKey to User)   
    - Description: The user who wrote the review   
    - On Delete: CASCADE   
3. `text` (TextField)   
    - Description: The content of the review   
4. `rating` (IntegerField)   
    - Description: The rating given by the user   
    - Validators: MinValueValidator(0), MaxValueValidator(10)   
    - Help Text: "Rating from 0 to 10"   
5. `created\_at` (DateTimeField)   
    - Description: The date and time when the review was created   
    - Auto Now Add: True   
6. `updated\_at` (DateTimeField)   
    - Description: The date and time when the review was last updated   
    - Auto Now: True   
   
### 1.2 Meta   
- `unique\_together`: ("villa", "user")   
    - This ensures that a user can only write one review per villa   
   
### 1.3 Methods   
1. `\_\_str\_\_(self)`   
    - Description: String representation of the Review object   
    - Returns: A string in the format "Review by {username} for {villa\_name}"   
   
## 2. Review Service   
The `ReviewService` class provides several methods to manage reviews:   
### 2.1 Methods   
1. `can\_review\_villa(user, villa)`   
    - Description: Checks if a user is eligible to review a villa   
    - Validation:   
        - Checks if the villa is approved for reviews   
        - Verifies that the user has a past, paid booking for the villa   
        - Ensures the user hasn't already reviewed this villa   
2. `create\_or\_update\_review(user, villa, review\_data)`   
    - Description: Creates a new review or updates an existing one   
    - Returns: The review object and a boolean indicating if it was created or updated   
3. `get\_villa\_reviews(villa)`   
    - Description: Retrieves all reviews for a specific villa   
    - Returns: QuerySet of Review objects   
4. `get\_user\_reviews(user)`   
    - Description: Retrieves all reviews written by a specific user   
    - Returns: QuerySet of Review objects   
   
## 3. Review Serializer   
The `ReviewSerializer` is responsible for serializing and deserializing Review objects:   
### 3.1 Fields   
- All fields from the Review model, except:   
    - `user` is read-only and uses StringRelatedField   
    - `id`, `created\_at`, `updated\_at`, `user`, and `villa` are read-only   
   
### 3.2 Methods   
1. `create(self, validated\_data)`   
    - Description: Creates a new review   
    - Uses the current user and villa from the context   
2. `update(self, instance, validated\_data)`   
    - Description: Updates an existing review   
    - Only updates `text` and `rating` fields   
   
## 4. Review ViewSet   
The `ReviewViewSet` handles API requests related to reviews:   
### 4.1 Permissions   
- List and Retrieve: AllowAny   
- Create, Update, Delete: IsAuthenticated   
   
### 4.2 Methods   
1. `get\_queryset(self)`   
    - Description: Returns reviews for a specific villa   
2. `create(self, request, \*args, \*\*kwargs)`   
    - Description: Creates a new review   
    - Checks if the user can review the villa   
    - Creates or updates the review   
3. `update(self, request, \*args, \*\*kwargs)`   
    - Description: Updates an existing review   
    - Checks if the user can review the villa   
4. `my\_reviews(self, request)`   
    - Description: Returns all reviews written by the current user   
5. `villa\_reviews(self, request)`   
    - Description: Returns all reviews for a specific villa   
   
## 5. API Endpoints   
1. `GET /api/villas/{villa\_slug}/reviews/`: List all reviews for a specific villa   
2. `POST /api/villas/{villa\_slug}/reviews/`: Create a new review for a villa   
3. `GET /api/villas/{villa\_slug}/reviews/{id}/`: Retrieve a specific review   
4. `PUT /api/villas/{villa\_slug}/reviews/{id}/`: Update a specific review   
5. `DELETE /api/villas/{villa\_slug}/reviews/{id}/`: Delete a specific review   
6. `GET /api/villas/{villa\_slug}/reviews/my\_reviews/`: List all reviews by the current user   
7. `GET /api/villas/{villa\_slug}/reviews/villa\_reviews/`: List all reviews for the current villa   
   
## 6. Business Rules   
1. A user can only review a villa if:   
    - The villa is approved for reviews   
    - The user has a past, paid booking for the villa   
    - The user hasn't already reviewed this villa   
2. A user can only have one review per villa   
3. Ratings must be between 0 and 10   
4. Users can update their own reviews   
5. Only authenticated users can create, update, or delete reviews   
6. Anyone can view reviews   
