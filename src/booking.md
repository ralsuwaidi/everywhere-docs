# Booking

Booking Model Documentation

## Fields

1. `customer` (ForeignKey to User model)
   - Description: The user who made the booking
   - On Delete: CASCADE
   - Help Text: "The user who made the booking."
2. `villa` (ForeignKey to Villa model)
   - Description: The villa that is booked
   - On Delete: CASCADE
   - Help Text: "The villa that the booking is for."
3. `start_date` (DateField)
   - Description: The start date of the booking
   - Help Text: "The starting date of the booking."
4. `end_date` (DateField)
   - Description: The end date of the booking
   - Help Text: "The ending date of the booking."
5. `guest_count` (PositiveIntegerField)
   - Description: The number of guests for this booking
   - Help Text: "The total number of guests for this booking."
6. `total_price` (DecimalField)
   - Description: The total price for the booking
   - Max Digits: 10
   - Decimal Places: 2
   - Help Text: "The total price for the booking."
7. `status` (CharField)
   - Description: The status of the booking
   - Max Length: 10
   - Choices:
     - PENDING: "PEN"
     - PAID: "PAI"
     - CANCELLED: "CANC"
   - Default: "PENDING"
   - Help Text: "The status of the booking."
8. `status_reason` (CharField)
   - Description: The reason for the booking status
   - Max Length: 255
   - Null: True
   - Blank: True
   - Help Text: "The reason the booking was confirmed or cancelled."

## Relationships

1. Many-to-Many relationship with Addon model
   - Field Name: `add_ons`
   - Related Name: Not specified
   - Blank: True
   - Help Text: "The add-ons selected for this booking."
2. ForeignKey relationship with Coupon model
   - Field Name: `coupon`
   - On Delete: SET_NULL
   - Null: True
   - Blank: True
   - Help Text: "The coupon applied to this booking."
3. Many-to-Many relationship with Deal model
   - Field Name: `deals`
   - Related Name: Not specified
   - Blank: True
   - Help Text: "The deals applied to this booking."
4. Many-to-Many relationship with TargetedNightDeal model
   - Field Name: `targeted_night_deals`
   - Related Name: Not specified
   - Blank: True
   - Help Text: "Targeted deals applied to specific nights of the booking."

## Methods

1. `calculate_nights(self)`
   - Description: Calculates the number of nights between the start date and end date of the booking
   - Returns: An integer representing the number of nights
2. `__str__(self)`
   - Description: String representation of the Booking object
   - Returns: A string containing the villa name, customer, start and end dates, and number of nights

## Notes

- The Booking model is central to the villa rental system, connecting users (customers) with villas and tracking the details of each reservation.
- It includes fields for managing the booking status, which can be useful for tracking the lifecycle of a booking (e.g., pending, paid, cancelled).
- The model allows for the application of various types of deals and add-ons, making it flexible for different pricing strategies.
- The `calculate_nights` method provides a convenient way to determine the duration of the booking.
- The inclusion of a `guest_count` field allows for tracking the number of guests separately from the booking duration.
- The `status_reason` field provides space for additional context about the booking's status, which can be useful for both administrative purposes and customer communication.  


## Usage in the System

- This model is used in conjunction with the BookingService for operations like creating bookings, calculating prices, and managing booking statuses.
- It interacts closely with the Villa model for availability checking and price calculations.
- The relationships with Deal, Coupon, and TargetedNightDeal models provides a complex pricing system that can apply various discounts and special offers to bookings.
