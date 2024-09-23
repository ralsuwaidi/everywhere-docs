# Deal Model

Deal Model Documentation

## 1. Deal Model

### 1.1 Fields

1. `name` (CharField)
   - Description: The name of the deal
   - Max Length: 255 characters
   - Unique: True
2. `villa` (ForeignKey to Villa)
   - Description: The villa this deal is associated with
   - Related Name: "deals"
3. `deal_type` (CharField)
   - Description: The type of deal (percentage discount or flat rate)
   - Max Length: 10 characters
   - Choices:
     - PERCENTAGE: "PERCENTAGE"
     - FLAT: "FLAT"
   - Default: "FLAT"
4. `discount_value` (DecimalField)
   - Description: The value of the discount
   - Max Digits: 10
   - Decimal Places: 2
   - Help Text: Represents a percentage for 'Percentage' deals and a flat rate amount for 'Flat Rate' deals
5. `display_start_date` (DateField)
   - Description: The date when the deal can be viewed by users
6. `display_end_date` (DateField)
   - Description: The date when the deal stops being viewed
7. `usage_start_date` (DateField)
   - Description: The date from which the deal can be used
8. `usage_end_date` (DateField)
   - Description: The final date when the deal can be used
9. `max_nights_of_deal` (PositiveIntegerField)
   - Description: The maximum number of nights the deal can apply
10. `min_nights` (PositiveIntegerField)
    - Description: The minimum number of nights to qualify for the deal
11. `applicable_days` (ArrayField of BooleanField)
    - Description: Array of booleans representing if the deal applies from Monday to Sunday
    - Size: 7 (one boolean for each day of the week)
    - Default: All True

### 1.2 Methods

1. `__str__(self)`
   - Description: String representation of the Deal object
   - Returns: A string containing the deal name and associated villa name

## 2. Deal Service

The `DealService` class provides several methods for managing deals:

### 2.1 Methods

1. `validate_deal_creation(deal_data: dict)`
   - Description: Validates the data for creating a new deal
   - Checks:
     - Date range validity for display and usage dates
     - Usage dates against display dates
     - At least one day is selected for deal applicability
2. `get_villa_details_with_deals(villa_slug, user)`
   - Description: Fetches villa details including filtered deals based on the user
   - Returns: Villa object and related deals
3. `check_for_overlapping_deals(villa, start_date, end_date)`
   - Description: Checks for any overlapping deals within the given date ranges for a specific villa
   - Returns: Boolean indicating if overlapping deals exist
4. `get_filtered_deals(user, villa_slug=None)`
   - Description: Fetches and returns a queryset of deals filtered based on the given user and optional villa slug
   - Filtering logic:
     - For staff users: all deals
     - For villa owners: all deals of their villas
     - For other users: only active deals based on current date and display dates
5. `is_deal_applicable(booking, deal: Deal) -> bool`
   - Description: Checks if the deal's applicable days and duration match the booking date range
   - Returns: Boolean indicating if the deal is applicable to the booking

## 3. Deal Serializer

The `DealSerializer` class handles the serialization and deserialization of Deal objects:

### 3.1 Fields

- All fields from the Deal model are included
- `villa` is set as a read-only field  


### 3.2 Validations

1. `validate_discount_value(value)`
   - Ensures the discount value is greater than zero
   - For percentage deals, ensures the value does not exceed 100%
2. `validate_display_start_date(value)`
   - Ensures the display start date is in the future
3. `validate_usage_start_date(value)`
   - Ensures the usage start date is in the future
4. `validate(data)`
   - Calls `DealService.validate_deal_creation(data)` for comprehensive validation

## 4. Deal ViewSet

The `DealViewSet` class handles API operations for deals:

### 4.1 Permissions

- Uses `IsAuthenticated` and `IsOwnerOrAdmin` permissions  


### 4.2 Methods

1. `get_queryset()`
   - Filters deals based on the villa_slug if provided
   - Uses `DealService.get_filtered_deals()` for filtering
2. `perform_create(serializer)`
   - Ensures the user has permission to create deals for the villa
   - Checks for overlapping deals before saving

## 5. API Endpoints

1. `GET /api/villas/{villa_slug}/deals/`: List deals for a specific villa
2. `POST /api/villas/{villa_slug}/deals/`: Create a new deal for a villa
3. `GET /api/villas/{villa_slug}/deals/{id}/`: Retrieve a specific deal
4. `PUT /api/villas/{villa_slug}/deals/{id}/`: Update a deal
5. `DELETE /api/villas/{villa_slug}/deals/{id}/`: Delete a deal

Note: Access to these endpoints is controlled by permissions, ensuring only villa owners and staff can manage deals.

## 6. Integration with Booking System

- Deals are applied in the `BookingService.calculate_price()` method
- The booking system ensures that deals and coupons are not applied simultaneously
- Deals are stored in the `BookingTransaction` for historical record-keeping  
  [User API Reference](user-api-reference.md)
