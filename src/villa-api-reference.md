# Villa API Reference

# Villa API

## 1. Create Villa

### Description

Create a new villa listing in the system.

### Endpoint

`POST /api/villas/`

### Authentication

Token required (Villa Owner or Admin)

### Request Body

```
{
  "name": "Seaside Escape",
  "description": "A charming seaside villa with stunning views.",
  "villa_email": "contact@seasideescape.com",
  "villa_phone_number": "555-1234-5678",
  "check_in_time": "15:00:00",
  "check_out_time": "11:00:00",
  "cancellation_policy": "Free cancellation up to 7 days before check-in.",
  "regulations": "No smoking. No pets.",
  "owner_has_insurance": true,
  "insurance_amount": 250000.00,
  "max_adult_guests": 6,
  "max_child_guests": 3,
  "area_sq_feet": 2000,
  "booking_advance_notice": 7,
  "guest_type": "AL",
  "rooms": [
    {
      "name": "Master Bedroom",
      "single_beds": 0,
      "double_beds": 0,
      "king_beds": 1,
      "queen_beds": 0,
      "max_occupancy": 2
    },
    {
      "name": "Guest Room",
      "single_beds": 2,
      "double_beds": 0,
      "king_beds": 0,
      "queen_beds": 0,
      "max_occupancy": 2
    }
  ]
}

```

| Field                  | Type    | Required | Description                                         |
| :--------------------- | :------ | :------- | :-------------------------------------------------- |
| name                   | string  | Yes      | Name of the villa                                   |
| description            | string  | Yes      | Detailed description of the villa                   |
| villa_email            | string  | Yes      | Contact email for the villa                         |
| villa_phone_number     | string  | Yes      | Contact phone number for the villa                  |
| check_in_time          | time    | Yes      | Default check-in time                               |
| check_out_time         | time    | Yes      | Default check-out time                              |
| cancellation_policy    | string  | No       | Cancellation policy details                         |
| regulations            | string  | No       | House rules and regulations                         |
| owner_has_insurance    | boolean | Yes      | Whether the owner has insurance                     |
| insurance_amount       | decimal | No       | Insurance amount if applicable                      |
| max_adult_guests       | integer | Yes      | Maximum number of adult guests allowed              |
| max_child_guests       | integer | Yes      | Maximum number of child guests allowed              |
| area_sq_feet           | decimal | No       | Total area of the villa in square feet              |
| booking_advance_notice | integer | Yes      | Minimum days of advance notice required for booking |
| guest_type             | string  | Yes      | Type of guests allowed (AL for All, FA for Family)  |
| rooms                  | array   | Yes      | Array of room objects for the villa                 |

### Response

### Success Response

**Code:** 201 CREATED  
**Content example:**

```
{
  "id": 1,
  "name": "Seaside Escape",
  "slug": "seaside-escape",
  "description": "A charming seaside villa with stunning views.",
  "villa_email": "contact@seasideescape.com",
  "villa_phone_number": "555-1234-5678",
  "check_in_time": "15:00:00",
  "check_out_time": "11:00:00",
  "cancellation_policy": "Free cancellation up to 7 days before check-in.",
  "regulations": "No smoking. No pets.",
  "owner_has_insurance": true,
  "insurance_amount": 250000.00,
  "max_adult_guests": 6,
  "max_child_guests": 3,
  "area_sq_feet": 2000,
  "booking_advance_notice": 7,
  "guest_type": "AL",
  "review_status": "DR",
  "rooms": [
    {
      "id": 1,
      "name": "Master Bedroom",
      "single_beds": 0,
      "double_beds": 0,
      "king_beds": 1,
      "queen_beds": 0,
      "max_occupancy": 2
    },
    {
      "id": 2,
      "name": "Guest Room",
      "single_beds": 2,
      "double_beds": 0,
      "king_beds": 0,
      "queen_beds": 0,
      "max_occupancy": 2
    }
  ]
}

```

### Error Response

**Code:** 400 BAD REQUEST  
**Content:**

```
{
  "name": ["A villa with this name already exists."],
  "villa_email": ["Enter a valid email address."]
}

```

### Notes

- The villa is initially created with a "draft" review status.
- The `slug` field is automatically generated from the villa name.
- At least one room must be provided when creating a villa.  


### Example Usage

```
curl -X POST \
  http://localhost:8000/api/villas/ \
  -H "Authorization: Token YOUR_AUTH_TOKEN_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Seaside Escape",
    "description": "A charming seaside villa with stunning views. Perfect for a family getaway or a romantic retreat.",
    "villa_email": "contact@seasideescape.com",
    "villa_phone_number": "555-1234-5678",
    "check_in_time": "15:00:00",
    "check_out_time": "11:00:00",
    "cancellation_policy": "Free cancellation up to 7 days before check-in.",
    "regulations": "No smoking inside the villa. Pets are welcome with prior approval.",
    "owner_has_insurance": true,
    "insurance_amount": 250000.00,
    "max_adult_guests": 6,
    "max_child_guests": 3,
    "area_sq_feet": 2000,
    "booking_advance_notice": 7,
    "guest_type": "AL",
    "rooms": [
      {
        "name": "Master Bedroom",
        "single_beds": 0,
        "double_beds": 0,
        "king_beds": 1,
        "queen_beds": 0,
        "max_occupancy": 2
      },
      {
        "name": "Guest Room 1",
        "single_beds": 2,
        "double_beds": 0,
        "king_beds": 0,
        "queen_beds": 0,
        "max_occupancy": 2
      }
    ]
  }'

```

## 2. Get Villa Details

### Description

Retrieve detailed information about a specific villa.

### Endpoint

`GET /api/villas/{slug}/`

### Authentication

No authentication required for approved villas. Owner or Admin authentication required for draft villas.

### Path Parameters

| Parameter | Type   | Required | Description                  |
| :-------- | :----- | :------- | :--------------------------- |
| slug      | string | Yes      | The unique slug of the villa |

### Response

### Success Response

**Code:** 200 OK  
**Content example:**

```
{
  "id": 1,
  "name": "Seaside Escape",
  "slug": "seaside-escape",
  "description": "A charming seaside villa with stunning views.",
  "villa_email": "contact@seasideescape.com",
  "villa_phone_number": "555-1234-5678",
  "check_in_time": "15:00:00",
  "check_out_time": "11:00:00",
  "cancellation_policy": "Free cancellation up to 7 days before check-in.",
  "regulations": "No smoking. No pets.",
  "owner_has_insurance": true,
  "insurance_amount": 250000.00,
  "max_adult_guests": 6,
  "max_child_guests": 3,
  "area_sq_feet": 2000,
  "booking_advance_notice": 7,
  "guest_type": "AL",
  "review_status": "AP",
  "rooms": [
    {
      "id": 1,
      "name": "Master Bedroom",
      "single_beds": 0,
      "double_beds": 0,
      "king_beds": 1,
      "queen_beds": 0,
      "max_occupancy": 2
    },
    {
      "id": 2,
      "name": "Guest Room",
      "single_beds": 2,
      "double_beds": 0,
      "king_beds": 0,
      "queen_beds": 0,
      "max_occupancy": 2
    }
  ],
  "amenities": [
    {"id": 1, "name": "Wi-Fi"},
    {"id": 2, "name": "Swimming Pool"}
  ],
  "images": [
    {"id": 1, "image": "https://example.com/villa_images/seaside_escape_1.jpg"},
    {"id": 2, "image": "https://example.com/villa_images/seaside_escape_2.jpg"}
  ]
}

```

### Error Response

**Code:** 404 NOT FOUND  
**Content:**

```
{
  "detail": "Not found."
}

```

### Notes

- The `villa_email` field is only visible to staff users.
- Amenities and images are included in the response if available.  


### Example Usage

```
curl -X GET http://localhost:8000/api/villas/seaside-escape/

```

##

## 3. Update Villa

**Description**  
Update the details of an existing villa. This endpoint allows partial updates to villa information.  
**Endpoint**  
`PATCH /api/villas/{slug}/`  
**Authentication**  
Token required (Villa Owner or Admin)  
**Path Parameters**  
| Parameter | Type | Required | Description |
|:----------|:-------|:---------|:---------------------------------------|
| slug | string | Yes | The unique slug of the villa to update |

**Request Body**  
All fields are optional. Only include the fields you want to update.

```
{
  "name": "Updated Seaside Villa",
  "description": "A newly renovated seaside villa with stunning ocean views.",
  "villa_email": "newemail@seasidevilla.com",
  "villa_phone_number": "+1234567890",
  "max_adult_guests": 8,
  "max_child_guests": 4,
  "area_sq_feet": 2500,
  "booking_advance_notice": 3
}

```

**Response**  
_Success Response_  
**Code:** 200 OK  
**Content example:**

```
{
  "id": 1,
  "name": "Updated Seaside Villa",
  "slug": "seaside-escape",
  "description": "A newly renovated seaside villa with stunning ocean views.",
  "villa_email": "newemail@seasidevilla.com",
  "villa_phone_number": "+1234567890",
  "check_in_time": "15:00:00",
  "check_out_time": "11:00:00",
  "cancellation_policy": "Full refund up to 7 days before check-in",
  "regulations": "No smoking, no pets",
  "owner_has_insurance": true,
  "insurance_amount": "100000.00",
  "review_status": "AP",
  "max_adult_guests": 8,
  "max_child_guests": 4,
  "area_sq_feet": "2500.00",
  "booking_advance_notice": 3,
  "amenities": [
    {"name": "Wi-Fi"},
    {"name": "Swimming Pool"}
  ],
  "rooms": [
    {
      "id": 1,
      "name": "Master Bedroom",
      "single_beds": 0,
      "double_beds": 1,
      "king_beds": 0,
      "queen_beds": 0,
      "max_occupancy": 2
    }
  ]
}

```

_Error Response_  
**Code:** 400 BAD REQUEST  
**Content:**

```
{
  "max_adult_guests": ["Ensure this value is less than or equal to 10."],
  "villa_phone_number": ["Enter a valid phone number."]
}

```

**Notes**

- Only the villa owner or an admin can update the villa details.
- The review status may change to "pending review" if certain fields are updated.
- Fields not included in the PATCH request will remain unchanged.  


**Example Usage**

```
curl -X PATCH \
  -H "Authorization: Token YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "description": "A newly renovated seaside villa with stunning ocean views.",
    "max_adult_guests": 8,
    "max_child_guests": 4
  }' \
  https://api.example.com/api/villas/seaside-escape/

```

This example updates the description and guest capacity of the villa.

## 4. Check Villa Availability

### Description

Check the availability of a villa for a specific date range.

### Endpoint

`GET /api/villas/{slug}/check_availability/`

### Authentication

No authentication required.

### Path Parameters

| Parameter | Type   | Required | Description                  |
| :-------- | :----- | :------- | :--------------------------- |
| slug      | string | Yes      | The unique slug of the villa |

### Query Parameters

| Parameter  | Type | Required | Description                                         |
| :--------- | :--- | :------- | :-------------------------------------------------- |
| start_date | date | Yes      | Start date of the desired stay (format: YYYY-MM-DD) |
| end_date   | date | Yes      | End date of the desired stay (format: YYYY-MM-DD)   |

### Response

### Success Response

**Code:** 200 OK  
**Content example:**

```
{
  "available": true
}

```

### Error Response

**Code:** 400 BAD REQUEST  
**Content:**

```
{
  "error": "Invalid date format. Please use YYYY-MM-DD."
}

```

### Notes

- This endpoint checks if the villa is available for the entire specified date range.
- It takes into account existing bookings and any blocked dates set by the owner.  


### Example Usage

```
curl -X GET "https://api.example.com/api/villas/seaside-escape/check_availability/?start_date=2024-07-01&end_date=2024-07-07"

```
