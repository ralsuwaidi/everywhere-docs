# Villa Creation and Editing

Villa Creation and Editing Documentation

## Overview

The villa creation and editing process allows users (specifically villa owners) to create new villas and modify existing ones. This process involves several steps and includes validation to ensure data integrity.

## Process

### 1. Villa Creation

1. The villa owner initiates a request to create a new villa.
2. The system validates the user's permissions.
3. The villa data is submitted, including basic details and room information.
4. The system creates the villa in a draft state.
5. The owner can add images to the villa.
6. The owner sets up the base price for the villa.
7. Once complete, the owner can submit the villa for approval.

### 2. Villa Editing

1. The villa owner or an admin accesses an existing villa.
2. They can modify various aspects of the villa (details, rooms, pricing, etc.).

## API Endpoints

### Villa Creation and Management

1. **Create Villa**
   - Endpoint: `POST /api/villas/`
   - Permissions: Authenticated users
   - Payload: [Villa](villa.md) details including rooms
2. **Retrieve Villa Details**
   - Endpoint: `GET /api/villas/{slug}/`
   - Permissions: Any user for approved villas, owner/admin for draft villas
3. **Update Villa**
   - Endpoint: `PUT /api/villas/{slug}/`
   - Permissions: Villa owner or admin
4. **Partial Update Villa**
   - Endpoint: `PATCH /api/villas/{slug}/`
   - Permissions: Villa owner or admin
5. **Submit Villa for Approval**
   - Endpoint: `POST /api/villas/{slug}/submit/`
   - Permissions: Villa owner or admin
6. **Upload Villa Images**
   - Endpoint: `POST /api/villas/{slug}/upload_images/`
   - Permissions: Villa owner or admin

### Room Management

1. **Create Room**
   - Endpoint: `POST /api/villas/{villa_slug}/rooms/`
   - Permissions: Authenticated users (villa owner)
2. **List Rooms**
   - Endpoint: `GET /api/villas/{villa_slug}/rooms/`
   - Permissions: Any user
3. **Update Room**
   - Endpoint: `PUT /api/villas/{villa_slug}/rooms/{room_id}/`
   - Permissions: Villa owner or admin

### Price Management

1. **Set Base Price**
   - Endpoint: `POST /api/villas/{villa_slug}/base-price/`
   - Permissions: Villa owner or admin
2. **Update Base Price**
   - Endpoint: `PUT /api/villas/{villa_slug}/base-price/`
   - Permissions: Villa owner or admin

## Validation

### Villa Creation/Editing

1. Name must be unique and not empty.
2. Valid email and phone number are required.
3. Check-in and check-out times must be valid time formats.
4. Max guest counts (adult and child) must be non-negative integers.
5. Area must be a positive decimal.
6. Booking advance notice must be a non-negative integer.

### Room Creation/Editing

1. Bed counts (single, double, king, queen) must be non-negative integers.
2. Max occupancy must be a positive integer.

### Image Upload

1. Image file size is limited (e.g., max 5MB).
2. Image dimensions are checked (e.g., max 1920x1080).

### Base Price

1. Exactly 7 prices must be provided (one for each day of the week).
2. All prices must be positive decimals.

### Villa Submission

Before a villa can be submitted for approval:

1. It must have at least one image.
2. A base price must be set.
3. The villa must be in draft status.

## Notes

- Villas are created in a draft state and must be submitted for approval before becoming visible to guests.
- Only approved villas are visible to non-owner users in general villa listings.
- Villa owners can view and edit their own villas regardless of approval status.
- Admins can view and edit all villas.
- The villa slug is automatically generated from the name and used in URLs.
- Room creation is part of the villa creation process, allowing for multiple rooms to be added at once.  


## Related Models

- [Villa](villa.md)
- Room
- VillaImage
- BasePrice
