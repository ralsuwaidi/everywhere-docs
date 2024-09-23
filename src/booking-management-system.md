# Booking Management System

Booking Management System Documentation

## 1. Booking Model

### 1.1 Fields

1. `customer` (ForeignKey to User)
   - Description: The user who made the booking
   - On Delete: CASCADE
2. `villa` (ForeignKey to Villa)
   - Description: The villa that the booking is for
   - On Delete: CASCADE
3. `start_date` (DateField)
   - Description: The starting date of the booking
4. `end_date` (DateField)
   - Description: The ending date of the booking
5. `guest_count` (PositiveIntegerField)
   - Description: The total number of guests for this booking
6. `total_price` (DecimalField)
   - Description: The total price for the booking
   - Max Digits: 10
   - Decimal Places: 2
7. `status` (CharField)
   - Description: The status of the booking
   - Max Length: 10
   - Choices:
     - PENDING: "PEN"
     - PAID: "PAI"
     - CANCELLED: "CANC"
   - Default: "PENDING"
8. `status_reason` (CharField)
   - Description: The reason the booking was confirmed or cancelled
   - Max Length: 255
   - Null: True
   - Blank: True
9. `add_ons` (ManyToManyField to Addon)
   - Description: The add-ons selected for this booking
   - Blank: True
10. `coupon` (ForeignKey to Coupon)
    - Description: The coupon applied to this booking
    - Null: True
    - Blank: True
    - On Delete: SET_NULL
11. `deals` (ManyToManyField to Deal)
    - Description: The deals applied to this booking
    - Blank: True
12. `targeted_night_deals` (ManyToManyField to TargetedNightDeal)
    - Description: Targeted deals applied to specific nights of the booking
    - Blank: True

### 1.2 Methods

1. `calculate_nights(self)`
   - Description: Calculates the number of nights between the start date and end date of the booking
   - Returns: An integer representing the number of nights
2. `__str__(self)`
   - Description: String representation of the Booking object
   - Returns: A string describing the booking, including villa name, customer, dates, and number of nights

## 2. Booking Process

### 2.1 Creating a Booking

The booking creation process is handled by the `BookingSerializer` and `BookingService`. Here's an overview of the process:

1. The client sends a POST request with booking details.
2. The `BookingSerializer` validates the input data:
   - Checks if the booking dates are logically consistent.
   - Ensures the booking respects the advance notice requirement of the villa.
   - Verifies if the villa is available for the given date range.
   - Validates that the booking is for future dates.
   - Checks if deals and coupons are not applied simultaneously.
3. If validation passes, the `BookingService` calculates the total price:
   - Applies base prices for each day.
   - Applies custom rates if applicable.
   - Applies general deals if any.
   - Applies targeted night deals if any.
   - Applies coupon if provided.
4. The booking is created and saved to the database.

### 2.2 Retrieving Bookings

1. `get_my_bookings(user)`: Retrieves all bookings for a specific user.
2. `get_upcoming_bookings(user)`: Retrieves future bookings for a specific user.

### 2.3 Cancelling a Booking

The `cancel_booking(user, booking_id)` method in `BookingService` handles booking cancellations:

1. Verifies that the user has permission to cancel the booking.
2. Changes the booking status to CANCELLED.
3. Updates the status_reason.

## 3. Price Calculation

The `calculate_price` method in `BookingService` handles complex price calculations:

1. Retrieves base prices for the villa.
2. Applies custom rates if applicable.
3. Applies general deals.
4. Applies targeted night deals.
5. Applies coupon if provided.

This method ensures that all pricing rules, deals, and discounts are correctly applied to each booking.

## 4. Booking Validation

The `BookingValidator` class provides several validation methods:

1. `validate_booking_dates`: Ensures end date is after start date.
2. `validate_villa_approved_status`: Checks if the villa is approved for bookings.
3. `validate_booking_date_with_advance_notice`: Ensures booking respects the villa's advance notice requirement.
4. `validate_no_deal_and_coupon`: Prevents simultaneous application of deals and coupons.
5. `check_villa_availability`: Verifies the villa is available for the requested dates.

## 5. Booking Transactions

The `BookingTransactionService` creates a transaction record for each booking:

1. Captures a snapshot of the villa, addons, coupons, and deals at the time of booking.
2. Stores transaction details including total price, transaction date, and payment information.

This allows for historical tracking of booking details, even if the related models change over time.

## 6. API Endpoints

1. `POST /api/bookings/`: Create a new booking
2. `GET /api/bookings/`: List all bookings (for staff users)
3. `GET /api/bookings/{id}/`: Retrieve a specific booking
4. `PUT /api/bookings/{id}/`: Update a booking
5. `DELETE /api/bookings/{id}/`: Cancel a booking
6. `GET /api/bookings/my_bookings/`: Retrieve the current user's bookings
7. `GET /api/bookings/upcoming_bookings/`: Retrieve the current user's future bookings

Note: Access to these endpoints is controlled by permissions, ensuring users can only access and modify their own bookings unless they have staff privileges.
