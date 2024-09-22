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
4. `villa\_email` (EmailField)   
    - Description: Contact email for the villa   
5. `villa\_phone\_number` (CharField)   
    - Description: Contact phone number for the villa   
    - Max Length: 15 characters   
6. `check\_in\_time` (TimeField)   
    - Description: Default check-in time   
    - Default: "14:00:00" (2 PM)   
7. `check\_out\_time` (TimeField)   
    - Description: Default check-out time   
    - Default: "12:00:00" (12 PM)   
8. `cancellation\_policy` (TextField)   
    - Description: The villa's cancellation policy   
    - Null: True   
    - Blank: True   
9. `regulations` (TextField)   
    - Description: Rules and regulations for the villa   
    - Null: True   
    - Blank: True   
10. `owner\_has\_insurance` (BooleanField)   
    - Description: Indicates if the owner has insurance for the villa   
    - Default: False   
11. `insurance\_amount` (DecimalField)   
    - Description: The amount of insurance coverage   
    - Max Digits: 10   
    - Decimal Places: 2   
    - Null: True   
    - Blank: True   
    - Validators: MinValueValidator(Decimal("0.00"))   
12. `review\_status` (CharField)   
    - Description: The current review status of the villa   
    - Max Length: 2 characters   
    - Choices:   
        - PENDING\_REVIEW: "PR"   
        - APPROVED: "AP"   
        - REJECTED: "RJ"   
    - Default: DRAFT ("DR")   
13. `max\_adult\_guests` (IntegerField)   
    - Description: Maximum number of adult guests allowed   
    - Null: True   
    - Blank: True   
14. `max\_child\_guests` (IntegerField)   
    - Description: Maximum number of child guests allowed   
    - Null: True   
    - Blank: True   
15. `area\_sq\_feet` (DecimalField)   
    - Description: The area of the villa in square feet   
    - Max Digits: 10   
    - Decimal Places: 2   
    - Null: True   
    - Blank: True   
16. `booking\_advance\_notice` (PositiveIntegerField)   
    - Description: Minimum number of days of advance notice required for booking   
    - Default: 0   
17. `slug` (SlugField)   
    - Description: URL-friendly version of the name   
    - Unique: True   
18. `guest\_type` (CharField)   
    - Description: Type of guests the villa is suitable for   
    - Max Length: 2 characters   
    - Choices:   
        - FAMILY: "FA"   
        - ALL: "AL"   
    - Default: ALL   
   
## Methods   
1. `save(self, \*args, \*\*kwargs)`   
    - Description: Overridden save method to automatically generate the slug   
2. `check\_room\_availability(self)`   
    - Description: Checks if the villa has any rooms available   
    - Raises: ValueError if no rooms are available   
3. `get\_absolute\_url(self)`   
    - Description: Returns the URL for the villa's detail view   
4. `is\_available(self, start\_date, end\_date)`   
    - Description: Checks if the villa is available for a given date range   
    - Returns: Boolean indicating availability   
5. `\_\_str\_\_(self)`   
    - Description: String representation of the Villa object   
    - Returns: The name of the villa   
   
## Properties   
1. `owner\_email`   
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
    - One-to-One with BasePrice model (base\_price)   
   
## Notes   
- The Villa model uses a slug field for URL-friendly identifiers.   
- It includes fields for managing guest limits, area, and booking notices.   
- The review status field allows for a moderation process before villas are publicly visible.   
- The model includes methods for checking availability and room status.   
- There's a custom save method to ensure the slug is always set.   
