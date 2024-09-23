# Villa

Villa Model Documentation

## Fields

1. `name` (CharField)
   - Description: The name of the villa
   - Max Length: 255 characters
   - Unique: True
2. `owner` (ForeignKey)
   - Description: The user who owns the villa
   - Related Model: User
   - On Delete: CASCADE
3. `description` (CharField)
   - Description: A description of the villa
   - Max Length: 2000 characters
   - Null: True
   - Blank: True
4. `villa_email` (EmailField)
   - Description: Contact email for the villa
5. `villa_phone_number` (CharField)
   - Description: Contact phone number for the villa
   - Max Length: 15 characters
6. `check_in_time` (TimeField)
   - Description: Default check-in time
   - Default: "14:00:00" (2 PM)
7. `check_out_time` (TimeField)
   - Description: Default check-out time
   - Default: "12:00:00" (12 PM)
8. `cancellation_policy` (TextField)
   - Description: The villa's cancellation policy
   - Null: True
   - Blank: True
9. `regulations` (TextField)
   - Description: Rules and regulations for the villa
   - Null: True
   - Blank: True
10. `owner_has_insurance` (BooleanField)
    - Description: Indicates if the owner has insurance for the villa
    - Default: False
11. `insurance_amount` (DecimalField)
    - Description: The amount of insurance coverage
    - Max Digits: 10
    - Decimal Places: 2
    - Null: True
    - Blank: True
    - Validators: MinValueValidator(Decimal("0.00"))
12. `review_status` (CharField)
    - Description: The current review status of the villa
    - Max Length: 2 characters
    - Choices:
      - PENDING_REVIEW: "PR"
      - APPROVED: "AP"
      - REJECTED: "RJ"
    - Default: DRAFT ("DR")
13. `max_adult_guests` (IntegerField)
    - Description: Maximum number of adult guests allowed
    - Null: True
    - Blank: True
14. `max_child_guests` (IntegerField)
    - Description: Maximum number of child guests allowed
    - Null: True
    - Blank: True
15. `area_sq_feet` (DecimalField)
    - Description: The area of the villa in square feet
    - Max Digits: 10
    - Decimal Places: 2
    - Null: True
    - Blank: True
16. `booking_advance_notice` (PositiveIntegerField)
    - Description: Minimum number of days of advance notice required for booking
    - Default: 0
17. `slug` (SlugField)
    - Description: URL-friendly version of the name
    - Unique: True
18. `guest_type` (CharField)
    - Description: Type of guests the villa is suitable for
    - Max Length: 2 characters
    - Choices:
      - FAMILY: "FA"
      - ALL: "AL"
    - Default: ALL

## Methods

1. `save(self, \*args, \*\*kwargs)`
   - Description: Overridden save method to automatically generate the slug
2. `check_room_availability(self)`
   - Description: Checks if the villa has any rooms available
   - Raises: ValueError if no rooms are available
3. `get_absolute_url(self)`
   - Description: Returns the URL for the villa's detail view
4. `is_available(self, start_date, end_date)`
   - Description: Checks if the villa is available for a given date range
   - Returns: Boolean indicating availability
5. `__str__(self)`
   - Description: String representation of the Villa object
   - Returns: The name of the villa

## Properties

1. `owner_email`
   - Description: Returns the email of the villa owner

## Relationships

1. ForeignKey to User model (owner)
2. ManyToMany with Amenity model (amenities)
3. Reverse relationships:
   - One-to-Many with Room model (rooms)
   - One-to-Many with VillaImage model (images)
   - One-to-Many with Booking model (bookings)
   - One-to-Many with Review model (reviews)
   - One-to-Many with Deal model (deals)
   - One-to-Many with Coupon model (coupons)
   - One-to-One with BasePrice model (base_price)

## Notes

- The Villa model uses a slug field for URL-friendly identifiers.
- It includes fields for managing guest limits, area, and booking notices.
- The review status field allows for a moderation process before villas are publicly visible.
- The model includes methods for checking availability and room status.
- There's a custom save method to ensure the slug is always set.
