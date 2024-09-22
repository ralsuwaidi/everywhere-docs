# Booking API Reference   
## 1. List Bookings   
Retrieves a list of bookings for the authenticated user.   
- **URL**: `/api/bookings/`   
- **Method**: GET   
- **Auth Required**: Yes   
   
### Response   
```
[
  {
    "id": 1,
    "villa": 1,
    "villa_name": "Seaside Villa",
    "villa_slug": "seaside-villa",
    "start_date": "2024-07-01",
    "end_date": "2024-07-07",
    "guest_count": 4,
    "total_price": "1400.00",
    "status": "PENDING",
    "status_reason": null,
    "add_ons": [],
    "coupon": null,
    "deals": []
  },
  // ... more bookings
]

```
## 2. Create Booking   
Creates a new booking for the authenticated user.   
- **URL**: `/api/bookings/`   
- **Method**: POST   
- **Auth Required**: Yes   
   
### Request Body   
```
{
  "villa": 1,
  "start_date": "2024-07-01",
  "end_date": "2024-07-07",
  "guest_count": 4,
  "add_ons": [1, 2],
  "coupon": "SUMMER2024",
  "deals": [1]
}

```
### Response   
```
{
  "id": 1,
  "villa": 1,
  "villa_name": "Seaside Villa",
  "villa_slug": "seaside-villa",
  "start_date": "2024-07-01",
  "end_date": "2024-07-07",
  "guest_count": 4,
  "total_price": "1260.00",
  "status": "PENDING",
  "status_reason": null,
  "add_ons": [1, 2],
  "coupon": "SUMMER2024",
  "deals": [1]
}

```
## 3. Retrieve Booking   
Retrieves details of a specific booking.   
- **URL**: `/api/bookings/{id}/`   
- **Method**: GET   
- **Auth Required**: Yes   
   
### Response   
```
{
  "id": 1,
  "villa": 1,
  "villa_name": "Seaside Villa",
  "villa_slug": "seaside-villa",
  "start_date": "2024-07-01",
  "end_date": "2024-07-07",
  "guest_count": 4,
  "total_price": "1260.00",
  "status": "PENDING",
  "status_reason": null,
  "add_ons": [1, 2],
  "coupon": "SUMMER2024",
  "deals": [1]
}

```
## 4. Update Booking   
Updates an existing booking. Note that some fields may not be editable after creation.   
- **URL**: `/api/bookings/{id}/`   
- **Method**: PUT   
- **Auth Required**: Yes   
   
### Request Body   
```
{
  "guest_count": 5,
  "add_ons": [1, 2, 3]
}

```
### Response   
Similar to the Retrieve Booking response, with updated fields.   
## 5. Cancel Booking   
Cancels an existing booking.   
- **URL**: `/api/bookings/{id}/cancel/`   
- **Method**: POST   
- **Auth Required**: Yes   
   
### Response   
```
{
  "id": 1,
  "status": "CANCELLED",
  "status_reason": "Cancelled by user",
  // ... other booking fields
}

```
## 6. My Bookings   
Retrieves all bookings for the currently authenticated user.   
- **URL**: `/api/bookings/my\_bookings/`   
- **Method**: GET   
- **Auth Required**: Yes   
   
### Response   
An array of booking objects, similar to the List Bookings response.   
## 7. Upcoming Bookings   
Retrieves upcoming bookings for the currently authenticated user.   
- **URL**: `/api/bookings/upcoming\_bookings/`   
- **Method**: GET   
- **Auth Required**: Yes   
   
### Response   
An array of upcoming booking objects, similar to the List Bookings response.   
## Notes   
- All dates should be in ISO 8601 format (YYYY-MM-DD).   
- The `total\_price` is calculated server-side and cannot be set directly by the client.   
- The `status` field is managed by the server and reflects the current state of the booking.   
- Coupons and deals are applied server-side and may affect the `total\_price`.   
- Add-ons are referenced by their IDs and must be valid for the selected villa.   
- Authentication is required for all booking-related operations.   
- Users can only access and modify their own bookings, except for staff users who can access all bookings.   
   
## Error Responses   
- 400 Bad Request: Invalid input data   
- 401 Unauthorized: Authentication required   
- 403 Forbidden: User doesn't have permission to perform the action   
- 404 Not Found: Booking or related resource not found   
- 409 Conflict: Booking dates conflict with existing bookings   
   
For detailed error messages, refer to the `errors` field in the JSON response body.   
