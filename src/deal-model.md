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
3. `deal\_type` (CharField)   
    - Description: The type of deal (percentage discount or flat rate)   
    - Max Length: 10 characters   
    - Choices:   
        - PERCENTAGE: "PERCENTAGE"   
        - FLAT: "FLAT"   
    - Default: "FLAT"   
4. `discount\_value` (DecimalField)   
    - Description: The value of the discount   
    - Max Digits: 10   
    - Decimal Places: 2   
    - Help Text: Represents a percentage for 'Percentage' deals and a flat rate amount for 'Flat Rate' deals   
5. `display\_start\_date` (DateField)   
    - Description: The date when the deal can be viewed by users   
6. `display\_end\_date` (DateField)   
    - Description: The date when the deal stops being viewed   
7. `usage\_start\_date` (DateField)   
    - Description: The date from which the deal can be used   
8. `usage\_end\_date` (DateField)   
    - Description: The final date when the deal can be used   
9. `max\_nights\_of\_deal` (PositiveIntegerField)   
    - Description: The maximum number of nights the deal can apply   
10. `min\_nights` (PositiveIntegerField)   
    - Description: The minimum number of nights to qualify for the deal   
11. `applicable\_days` (ArrayField of BooleanField)   
    - Description: Array of booleans representing if the deal applies from Monday to Sunday   
    - Size: 7 (one boolean for each day of the week)   
    - Default: All True   
   
### 1.2 Methods   
1. `\_\_str\_\_(self)`   
    - Description: String representation of the Deal object   
    - Returns: A string containing the deal name and associated villa name   
   
## 2. Deal Service   
The `DealService` class provides several methods for managing deals:   
### 2.1 Methods   
1. `validate\_deal\_creation(deal\_data: dict)`   
    - Description: Validates the data for creating a new deal   
    - Checks:   
        - Date range validity for display and usage dates   
        - Usage dates against display dates   
        - At least one day is selected for deal applicability   
2. `get\_villa\_details\_with\_deals(villa\_slug, user)`   
    - Description: Fetches villa details including filtered deals based on the user   
    - Returns: Villa object and related deals   
3. `check\_for\_overlapping\_deals(villa, start\_date, end\_date)`   
    - Description: Checks for any overlapping deals within the given date ranges for a specific villa   
    - Returns: Boolean indicating if overlapping deals exist   
4. `get\_filtered\_deals(user, villa\_slug=None)`   
    - Description: Fetches and returns a queryset of deals filtered based on the given user and optional villa slug   
    - Filtering logic:   
        - For staff users: all deals   
        - For villa owners: all deals of their villas   
        - For other users: only active deals based on current date and display dates   
5. `is\_deal\_applicable(booking, deal: Deal) -> bool`   
    - Description: Checks if the deal's applicable days and duration match the booking date range   
    - Returns: Boolean indicating if the deal is applicable to the booking   
   
## 3. Deal Serializer   
The `DealSerializer` class handles the serialization and deserialization of Deal objects:   
### 3.1 Fields   
- All fields from the Deal model are included   
- `villa` is set as a read-only field   
   
### 3.2 Validations   
1. `validate\_discount\_value(value)`   
    - Ensures the discount value is greater than zero   
    - For percentage deals, ensures the value does not exceed 100%   
2. `validate\_display\_start\_date(value)`   
    - Ensures the display start date is in the future   
3. `validate\_usage\_start\_date(value)`   
    - Ensures the usage start date is in the future   
4. `validate(data)`   
    - Calls `DealService.validate\_deal\_creation(data)` for comprehensive validation   
   
## 4. Deal ViewSet   
The `DealViewSet` class handles API operations for deals:   
### 4.1 Permissions   
- Uses `IsAuthenticated` and `IsOwnerOrAdmin` permissions   
   
### 4.2 Methods   
1. `get\_queryset()`   
    - Filters deals based on the villa\_slug if provided   
    - Uses `DealService.get\_filtered\_deals()` for filtering   
2. `perform\_create(serializer)`   
    - Ensures the user has permission to create deals for the villa   
    - Checks for overlapping deals before saving   
   
## 5. API Endpoints   
1. `GET /api/villas/{villa\_slug}/deals/`: List deals for a specific villa   
2. `POST /api/villas/{villa\_slug}/deals/`: Create a new deal for a villa   
3. `GET /api/villas/{villa\_slug}/deals/{id}/`: Retrieve a specific deal   
4. `PUT /api/villas/{villa\_slug}/deals/{id}/`: Update a deal   
5. `DELETE /api/villas/{villa\_slug}/deals/{id}/`: Delete a deal   
   
Note: Access to these endpoints is controlled by permissions, ensuring only villa owners and staff can manage deals.   
## 6. Integration with Booking System   
- Deals are applied in the `BookingService.calculate\_price()` method   
- The booking system ensures that deals and coupons are not applied simultaneously   
- Deals are stored in the `BookingTransaction` for historical record-keeping   
[User API Reference](user-api-reference.md)    
