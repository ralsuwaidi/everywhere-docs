# Deal and Coupon API Reference   
##    
# Deal and Coupon API Reference   
## Deals API   
Base URL: `/api/villas/{villa\_slug}/deals/`   
### 1. List Deals   
Retrieves a list of deals for a specific villa.   
- **URL:** `/api/villas/{villa\_slug}/deals/`   
- **Method:** GET   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
- **Authentication:** Required   
- **Permissions:**   
    - Villa owners can view all their deals   
    - Staff users can view all deals   
    - Regular users can only view active deals (based on display dates)   
   
```
[
  {
    "id": 1,
    "name": "Summer Discount",
    "deal_type": "PERCENTAGE",
    "discount_value": 20.00,
    "display_start_date": "2023-06-01",
    "display_end_date": "2023-08-31",
    "usage_start_date": "2023-06-01",
    "usage_end_date": "2023-08-31",
    "max_nights_of_deal": 7,
    "min_nights": 3,
    "applicable_days": [true, true, true, true, true, true, true]
  },
  // … more deals
]

```
### 2. Create Deal   
Creates a new deal for a specific villa.   
- **URL:** `/api/villas/{villa\_slug}/deals/`   
- **Method:** POST   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
- **Authentication:** Required   
- **Permissions:** Villa owner or staff   
   
```
{
  "name": "Winter Special",
  "deal_type": "FLAT",
  "discount_value": 50.00,
  "display_start_date": "2023-12-01",
  "display_end_date": "2024-02-29",
  "usage_start_date": "2023-12-01",
  "usage_end_date": "2024-02-29",
  "max_nights_of_deal": 14,
  "min_nights": 2,
  "applicable_days": [true, true, true, true, true, true, true]
}
```
- **Response:** Created deal object   
   
### 3. Retrieve Deal   
Retrieves details of a specific deal.   
- **URL:** `/api/villas/{villa\_slug}/deals/{deal\_id}/`   
- **Method:** GET   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
    - `deal\_id`: The ID of the deal   
- **Authentication:** Required   
- **Permissions:**   
    - Villa owners can view their deals   
    - Staff users can view all deals   
    - Regular users can only view active deals   
- **Response:** Deal object   
   
### 4. Update Deal   
Updates an existing deal.   
- **URL:** `/api/villas/{villa\_slug}/deals/{deal\_id}/`   
- **Method:** PUT/PATCH   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
    - `deal\_id`: The ID of the deal   
- **Authentication:** Required   
- **Permissions:** Villa owner or staff   
- **Request Body:** Deal object (full or partial)   
- **Response:** Updated deal object   
   
### 5. Delete Deal   
Deletes a specific deal.   
- **URL:** `/api/villas/{villa\_slug}/deals/{deal\_id}/`   
- **Method:** DELETE   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
    - `deal\_id`: The ID of the deal   
- **Authentication:** Required   
- **Permissions:** Villa owner or staff   
- **Response:** 204 No Content   
   
## Coupons API   
Base URL: `/api/villas/{villa\_slug}/coupons/`   
### 1. List Coupons   
Retrieves a list of coupons for a specific villa.   
- **URL:** `/api/villas/{villa\_slug}/coupons/`   
- **Method:** GET   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
- **Authentication:** Required   
- **Permissions:**   
    - Villa owners can view all their coupons   
    - Staff users can view all coupons   
    - Regular users can view coupons targeted to them   
   
    ```
[
  {
    "id": 1,
    "name": "SUMMER2023",
    "apply_start_date": "2023-06-01",
    "apply_end_date": "2023-08-31",
    "usage_start_date": "2023-06-01",
    "usage_end_date": "2023-08-31",
    "max_users": 100,
    "discount_type": "PERCENTAGE",
    "discount_value": 15.00,
    "min_days": 3,
    "max_days": 14,
    "first_time_booking_only": false,
    "applicable_days": [true, true, true, true, true, true, true]
  },
  // … more coupons
]

```
   
### 2. Create Coupon   
Creates a new coupon for a specific villa.   
- **URL:** `/api/villas/{villa\_slug}/coupons/`   
- **Method:** POST   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
- **Authentication:** Required   
- **Permissions:** Villa owner or staff   
   
```
{
  "name": "NEWYEAR2024",
  "apply_start_date": "2023-12-01",
  "apply_end_date": "2024-01-31",
  "usage_start_date": "2023-12-01",
  "usage_end_date": "2024-01-31",
  "max_users": 50,
  "discount_type": "FLAT",
  "discount_value": 100.00,
  "min_days": 2,
  "max_days": 7,
  "first_time_booking_only": true,
  "applicable_days": [true, true, true, true, true, true, true]
}
```
- 
   
- **Response:** Created coupon object   
   
### 3. Retrieve Coupon   
Retrieves details of a specific coupon.   
- **URL:** `/api/villas/{villa\_slug}/coupons/{coupon\_id}/`   
- **Method:** GET   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
    - `coupon\_id`: The ID of the coupon   
- **Authentication:** Required   
- **Permissions:**   
    - Villa owners can view their coupons   
    - Staff users can view all coupons   
    - Regular users can view coupons targeted to them   
- **Response:** Coupon object   
   
### 4. Update Coupon   
Updates an existing coupon.   
- **URL:** `/api/villas/{villa\_slug}/coupons/{coupon\_id}/`   
- **Method:** PUT/PATCH   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
    - `coupon\_id`: The ID of the coupon   
- **Authentication:** Required   
- **Permissions:** Villa owner or staff   
- **Request Body:** Coupon object (full or partial)   
- **Response:** Updated coupon object   
   
### 5. Delete Coupon   
Deletes a specific coupon.   
- **URL:** `/api/villas/{villa\_slug}/coupons/{coupon\_id}/`   
- **Method:** DELETE   
- **URL Parameters:**   
    - `villa\_slug`: The slug of the villa   
    - `coupon\_id`: The ID of the coupon   
- **Authentication:** Required   
- **Permissions:** Villa owner or staff   
- **Response:** 204 No Content   
   
## Notes   
- All endpoints require authentication.   
- Permissions vary based on the user's role (villa owner, staff, or regular user).   
- Date fields should be in ISO 8601 format (YYYY-MM-DD).   
- The `applicable\_days` field is an array of 7 boolean values, representing Monday to Sunday.   
- Deals and coupons cannot overlap for the same villa and date range.   
- Validation is performed to ensure logical consistency (e.g., end dates after start dates, valid discount values).   
