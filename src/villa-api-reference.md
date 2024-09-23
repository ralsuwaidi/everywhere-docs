# Villa API Reference

## Base URL

`/api/villas/`

## Endpoints

### 1. List Villas

- **URL:** `/`
- **Method:** `GET`
- **Description:** Retrieves a list of villas based on the user's permissions.
- **Authentication:** Optional
- **Permissions:**
  - Authenticated users see approved villas and their own villas
  - Staff users see all villas
  - Unauthenticated users see only approved villas
- **Response:** List of villa objects  


### 2. Retrieve Villa Details

- **URL:** `/<slug>/`
- **Method:** `GET`
- **Description:** Retrieves detailed information about a specific villa.
- **Authentication:** Optional
- **Parameters:**
  - `slug` (string): The unique slug identifier of the villa
- **Response:** Detailed villa object including rooms, amenities, and deals (if applicable)  


### 3. Create Villa

- **URL:** `/`
- **Method:** `POST`
- **Description:** Creates a new villa.
- **Authentication:** Required
- **Permissions:** Authenticated users
- **Request Body:** Villa details (name, description, etc.)
- **Response:** Created villa object  


### 4. Update Villa

- **URL:** `/<slug>/`
- **Method:** `PUT`, `PATCH`
- **Description:** Updates an existing villa.
- **Authentication:** Required
- **Permissions:** Villa owner or admin
- **Parameters:**
  - `slug` (string): The unique slug identifier of the villa
- **Request Body:** Updated villa details
- **Response:** Updated villa object  


### 5. Approve Villa

- **URL:** `/<slug>/approve_villa/`
- **Method:** `POST`
- **Description:** Approves a villa (changes status to approved).
- **Authentication:** Required
- **Permissions:** Admin users only
- **Parameters:**
  - `slug` (string): The unique slug identifier of the villa
- **Response:** Updated villa object with approved status  


### 6. Submit Villa for Review

- **URL:** `/<slug>/submit/`
- **Method:** `POST`
- **Description:** Submits a villa for review (changes status to pending review).
- **Authentication:** Required
- **Permissions:** Villa owner or admin
- **Parameters:**
  - `slug` (string): The unique slug identifier of the villa
- **Response:** Updated villa object with pending review status  


### 7. List User's Villas

- **URL:** `/my_villas/`
- **Method:** `GET`
- **Description:** Retrieves a list of villas owned by the authenticated user.
- **Authentication:** Required
- **Response:** List of villa objects owned by the user  


### 8. Upload Villa Images

- **URL:** `/<slug>/upload_images/`
- **Method:** `POST`
- **Description:** Uploads images for a specific villa.
- **Authentication:** Required
- **Permissions:** Villa owner or read-only users
- **Parameters:**
  - `slug` (string): The unique slug identifier of the villa
- **Request Body:** Form-data with images
- **Response:** 201 Created status on success  


### 9. Check Villa Availability

- **URL:** `/<slug>/check_availability/`
- **Method:** `GET`
- **Description:** Checks if a villa is available for a given date range.
- **Authentication:** Optional
- **Parameters:**
  - `slug` (string): The unique slug identifier of the villa
  - `start_date` (string): Start date of the period to check (YYYY-MM-DD)
  - `end_date` (string): End date of the period to check (YYYY-MM-DD)
- **Response:** Availability status  


### 10. Get Villa Price

- **URL:** `/<slug>/price/`
- **Method:** `GET`
- **Description:** Calculates the price for a villa for a given date range.
- **Authentication:** Optional
- **Parameters:**
  - `slug` (string): The unique slug identifier of the villa
  - `start_date` (string): Start date of the stay (YYYY-MM-DD)
  - `end_date` (string): End date of the stay (YYYY-MM-DD)
- **Response:** Calculated price for the specified period  


### 11. Get Villa Calendar

- **URL:** `/<slug>/calendar/`
- **Method:** `GET`
- **Description:** Retrieves calendar data for a villa, including prices and availability.
- **Authentication:** Optional
- **Parameters:**
  - `slug` (string): The unique slug identifier of the villa
  - `start_date` (string): Start date for the calendar data (YYYY-MM-DD)
  - `end_date` (string): End date for the calendar data (YYYY-MM-DD)
- **Response:** Calendar data including prices for each day in the specified range  


## Notes

- All endpoints return appropriate HTTP status codes (200 for success, 400 for bad requests, 403 for permission denied, etc.).
- The API uses token-based authentication.
- Some endpoints have different behavior based on the user's role (owner, staff, or regular user).
- The villa slug is used as the identifier for most villa-specific operations instead of an ID.
