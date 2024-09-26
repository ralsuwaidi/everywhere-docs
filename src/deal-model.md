# Deal Model   
Deal Model Documentation   
## Overview   
The Deal model represents promotional offers or discounts that can be applied to villa bookings in the Everywhere system. It allows for flexible deal configurations, including percentage-based and flat-rate discounts, time-based applicability, and day-specific applications.   
## Fields   
1. **name** (CharField)   
    - Max length: 255 characters   
    - Unique: True   
    - Description: The name of the deal   
2. **villa** (ForeignKey)   
    - Related model: Villa   
    - On delete: CASCADE   
    - Related name: 'deals'   
    - Description: The villa to which this deal is associated   
3. **deal\_type** (CharField)   
    - Max length: 10 characters   
    - Choices:   
        - PERCENTAGE: "Percentage"   
        - FLAT: "Flat Rate"   
    - Default: "FLAT"   
    - Description: Specifies whether the deal is a percentage discount or a flat rate discount   
4. **discount\_value** (DecimalField)   
    - Max digits: 10   
    - Decimal places: 2   
    - Description: The value of the discount. For percentage deals, this represents a percentage (e.g., 15.00 for 15%). For flat rate deals, this represents a currency amount.   
5. **display\_start\_date** (DateField)   
    - Description: The date from which the deal becomes visible to users   
6. **display\_end\_date** (DateField)   
    - Description: The date until which the deal remains visible to users   
7. **usage\_start\_date** (DateField)   
    - Description: The date from which the deal can be applied to bookings   
8. **usage\_end\_date** (DateField)   
    - Description: The last date on which the deal can be applied to bookings   
9. **max\_nights\_of\_deal** (PositiveIntegerField)   
    - Description: The maximum number of nights to which the deal can be applied in a single booking   
10. **min\_nights** (PositiveIntegerField)   
    - Description: The minimum number of nights required in a booking for the deal to be applicable   
11. **applicable\_days** (ArrayField)   
    - Base field: BooleanField   
    - Size: 7   
    - Description: An array of 7 boolean values representing which days of the week the deal is applicable. Index 0 corresponds to Monday, and index 6 to Sunday.   
   
## Methods   
### str(self)   
Returns a string representation of the Deal object.   
```
def __str__(self):
    return f"{self.name} for {self.villa.name}"

```
## Relationships   
1. **Many-to-One with Villa**   
    - Each deal is associated with one villa   
    - A villa can have multiple deals (accessible via `villa.deals.all()`)   
2. **Many-to-Many with Booking**    
    - A deal can be applied to multiple bookings   
    - A booking can have multiple deals applied   
   
## Usage in the System   
1. **Deal Creation**: Used in the DealViewSet to create new deals for villas.   
2. **Price Calculation**: Referenced in the BookingService to apply discounts during booking price calculations.   
3. **Deal Listing**: Used in the DealViewSet to list and filter deals based on various criteria.   
   
## Validation   
The Deal model employs several validation mechanisms, primarily implemented in the DealSerializer and DealService. Here are the specific validations used in the system:   
### DealSerializer Validations   
1. **Discount Value Validation**   
    ```
def validate_discount_value(self, value):
    deal_type = self.initial_data.get("deal_type")
    if value <= 0:
        raise serializers.ValidationError("Discount value must be greater than zero.")
    if deal_type == "PERCENTAGE" and value > 100:
        raise serializers.ValidationError("Percentage discount value cannot exceed 100%.")
    return value

```
    - Ensures the discount value is positive for all deal types.   
    - For percentage deals, ensures the discount value does not exceed 100%.   
2. **Overall Data Validation**   
    ```
def validate(self, data):
    DealService.validate_deal_creation(data)
    return data

```
    - Delegates to the DealService for additional validations.   
   
### DealService Validations   
The `validate\_deal\_creation` method in DealService performs several checks:   
1. **Date Range Validation**   
    ```
@staticmethod
def validate_date_range(start_date, end_date, date_type):
    if start_date >= end_date:
        raise ValidationError(f"{date_type} start date must be before {date_type.lower()} end date.")

```
    - Ensures that the start date is before the end date for both display and usage date ranges.   
2. **Usage Against Display Validation**   
    ```
@staticmethod
def validate_usage_against_display(display_start, display_end, usage_start, usage_end):
    if usage_start < display_start or usage_end > display_end:
        raise ValidationError("Usage dates must be within display dates.")

```
    - Verifies that the usage date range is within the display date range.   
3. **Applicable Days Validation**   
    ```
if not any(deal_data.get("applicable_days", [])):
    raise ValidationError("At least one day must be selected for the deal to be applicable.")

```
    - Ensures that at least one day is selected in the `applicable\_days` field.   
   
### Overlapping Deals Check   
In the DealViewSet, there's an additional check for overlapping deals:   
```
if DealService.check_for_overlapping_deals(
    villa,
    serializer.validated_data["usage_start_date"],
    serializer.validated_data["usage_end_date"]
):
    raise ValidationError("An overlapping deal exists for the specified dates.")

```
This prevents the creation of deals that overlap with existing deals for the same villa.   
## Additional Validation Notes   
- The system ensures that only villa owners or admin users can create deals for a villa.   
- The unique constraint on the `name` field (defined in the model) ensures that each deal has a unique name across the entire system.   
- Date validations ensure logical consistency between display and usage dates, preventing scenarios where a deal could be used outside its display period.   
- The overlapping deals check prevents conflicts in deal applications and ensures clear pricing for users.   
   
## Indexing (todo)   
Indexes to improve query performance:   
```
class Meta:
    indexes = [
        models.Index(fields=['villa', 'usage_start_date', 'usage_end_date']),
        models.Index(fields=['deal_type']),
    ]

```
## Notes   
- The separation of display dates and usage dates allows for marketing deals in advance of their actual applicability.   
- The `applicable\_days` field provides flexibility in creating deals that apply only on specific days of the week (e.g., weekday discounts, weekend specials).   
