# TargetedNightDeal   
TargetedNightDeal Model Documentation   
The TargetedNightDeal model represents specialized promotional offers that apply discounts to specific nights of a guest's stay in villas within the Everywhere system. Unlike regular deals or coupons, these deals focus on particular nights within a reservation, allowing for more nuanced and strategic discounting strategies.   
### Fields   
1. `name` (CharField)   
    - Max length: 255 characters   
    - Unique: True   
    - Description: The name of the targeted night deal   
2. `villa` (ForeignKey)   
    - Related model: Villa   
    - On delete: CASCADE   
    - Related name: 'targeted\_night\_deals'   
    - Description: The villa to which this targeted night deal is associated   
3. `deal\_type` (CharField)   
    - Max length: 10 characters   
    - Choices:   
        - PERCENTAGE: "Percentage"   
        - FLAT: "Flat Rate"   
    - Default: "FLAT"   
    - Description: Specifies whether the deal is a percentage discount or a flat rate discount   
4. `discount\_value` (DecimalField)   
    - Max digits: 10   
    - Decimal places: 2   
    - Description: The value of the discount. For percentage deals, this represents a percentage (e.g., 100.00 for 100%). For flat rate deals, this represents a currency amount.   
5. `display\_start\_date` (DateField)   
    - Description: The date from which the deal becomes visible to users   
6. `display\_end\_date` (DateField)   
    - Description: The date until which the deal remains visible to users   
7. `usage\_start\_date` (DateField)   
    - Description: The date from which the deal can be applied to bookings   
8. `usage\_end\_date` (DateField)   
    - Description: The last date on which the deal can be applied to bookings   
9. `target\_night` (PositiveIntegerField)   
    - Description: The specific night of the stay to which the discount applies (e.g., 3 for "3rd night free")   
   
### Methods   
### \_\_str\_\_(self)   
Returns a string representation of the TargetedNightDeal object.   
```
def __str__(self):
    return f"{self.name} for {self.villa.name} (Night {self.target_night})"

```
### Relationships   
1. Many-to-One with Villa   
    - Each targeted night deal is associated with one villa   
    - A villa can have multiple targeted night deals (accessible via `villa.targeted\_night\_deals.all()`)   
   
### Usage in the System   
1. Deal Creation: Used in the TargetedNightDealViewSet to create new targeted night deals for villas.   
2. Price Calculation: Referenced in the BookingService to apply discounts during booking price calculations, specifically for the targeted nights.   
3. Deal Listing: Used in the TargetedNightDealViewSet to list and filter targeted night deals based on various criteria.   
   
### Validation   
The TargetedNightDeal model employs several validation mechanisms, primarily implemented in the TargetedNightDealSerializer and TargetedNightDealService. Here are the specific validations used in the system:   
### TargetedNightDealSerializer Validations   
1. Discount Value Validation   
   
```
def validate_discount_value(self, value):
    deal_type = self.initial_data.get("deal_type")
    if value <= 0:
        raise serializers.ValidationError("Discount value must be greater than zero.")
    if deal_type == "PERCENTAGE" and value > 100:
        raise serializers.ValidationError("Percentage discount value cannot exceed 100%.")
    return value

```
1. Target Night Validation   
   
```
def validate_target_night(self, value):
    if value < 1:
        raise serializers.ValidationError("The target night must be one or greater.")
    return value

```
1. Overall Data Validation   
   
```
def validate(self, data):
    TargetedNightDealService.validate_targeted_night_deal_creation(data)
    return data

```
### TargetedNightDealService Validations   
The `validate\_targeted\_night\_deal\_creation` method in TargetedNightDealService performs several checks:   
1. Date Range Validation   
   
```
@staticmethod
def validate_date_range(start_date, end_date, date_type):
    if start_date >= end_date:
        raise ValidationError(f"{date_type} start date must be before {date_type.lower()} end date.")

```
1. Usage Against Display Validation   
   
```
@staticmethod
def validate_usage_against_display(display_start, display_end, usage_start, usage_end):
    if usage_start < display_start or usage_end > display_end:
        raise ValidationError("Usage dates must be within display dates.")

```
### Overlapping Deals Check   
In the TargetedNightDealViewSet, there's an additional check for overlapping deals:   
```
if TargetedNightDealService.check_for_overlapping_deals(
    villa,
    serializer.validated_data["usage_start_date"],
    serializer.validated_data["usage_end_date"],
    serializer.validated_data["target_night"]
):
    raise ValidationError("An overlapping targeted night deal exists for the specified dates and target night.")

```
### Additional Validation Notes   
- The system ensures that only villa owners or admin users can create targeted night deals for a villa.   
- The unique constraint on the `name` field (defined in the model) ensures that each targeted night deal has a unique name across the entire system.   
- Date validations ensure logical consistency between display and usage dates, preventing scenarios where a deal could be used outside its display period.   
- The overlapping deals check prevents conflicts in deal applications and ensures clear pricing for users.   
   
### Indexing (todo)   
Indexes to improve query performance:   
```
class Meta:
    indexes = [
        models.Index(fields=['villa', 'usage_start_date', 'usage_end_date', 'target_night']),
        models.Index(fields=['deal_type']),
    ]

```
### Notes   
- The separation of display dates and usage dates allows for marketing deals in advance of their actual applicability.   
- The `target\_night` field enables the creation of promotions like "3rd night free" or "50% off the 5th night", which can be effective in encouraging longer stays.   
- Unlike regular deals, targeted night deals focus on specific nights of a stay, allowing for more specific discounting.   
