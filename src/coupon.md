# Coupon   
Coupon Model Documentation   
The Coupon model represents promotional codes or discounts that can be applied to villa bookings in the Everywhere system. It allows for flexible coupon configurations, including percentage-based and flat-rate discounts, time-based applicability, user-specific targeting, and day-specific applications.   
### Fields   
1. `name` (CharField)   
    - Max length: 255 characters   
    - Unique: True   
    - Description: The unique name or code of the coupon   
2. `villa` (ForeignKey)   
    - Related model: Villa   
    - On delete: CASCADE   
    - Description: The villa to which this coupon is associated   
3. `apply\_start\_date` (DateField)   
    - Description: The date from which the coupon can be applied to bookings   
4. `apply\_end\_date` (DateField)   
    - Description: The last date on which the coupon can be applied to bookings   
5. `usage\_start\_date` (DateField)   
    - Description: The date from which the coupon can be used for stays   
6. `usage\_end\_date` (DateField)   
    - Description: The last date on which the coupon can be used for stays   
7. `max\_users` (PositiveIntegerField)   
    - Description: The maximum number of users who can use this coupon   
8. `applied\_users` (ManyToManyField)   
    - Related model: User   
    - Related name: 'used\_coupons'   
    - Blank: True   
    - Description: Users who have already used this coupon   
9. `targeted\_users` (ManyToManyField)   
    - Related model: User   
    - Related name: 'targeted\_coupons'   
    - Blank: True   
    - Description: Users for whom this coupon is specifically intended   
10. `discount\_type` (CharField)   
    - Max length: 10 characters   
    - Choices:   
        - FLAT: "Flat Rate"   
        - PERCENTAGE: "Percentage"   
    - Default: "FLAT"   
    - Description: Specifies whether the coupon is a percentage discount or a flat rate discount   
11. `discount\_value` (DecimalField)   
    - Max digits: 8   
    - Decimal places: 2   
    - Description: The value of the discount. For percentage coupons, this represents a percentage (e.g., 15.00 for 15%). For flat rate coupons, this represents a currency amount.   
12. `min\_days` (PositiveIntegerField)   
    - Description: The minimum number of days required in a booking for the coupon to be applicable   
13. `max\_days` (PositiveIntegerField)   
    - Description: The maximum number of days in a booking for which the coupon can be applied   
14. `first\_time\_booking\_only` (BooleanField)   
    - Default: False   
    - Description: If True, the coupon can only be used by users making their first booking   
15. `applicable\_days` (ArrayField)   
    - Base field: BooleanField   
    - Size: 7   
    - Description: An array of 7 boolean values representing which days of the week the coupon is applicable. Index 0 corresponds to Monday, and index 6 to Sunday.   
   
### Methods   
### str(self)   
Returns a string representation of the Coupon object.   
```
def __str__(self):
    return f"{self.name} for {self.villa.name}"

```
### is\_active (property)   
Checks if the coupon is currently active based on the usage dates.   
```
@property
def is_active(self):
    today = timezone.now().date()
    return self.usage_start_date <= today <= self.usage_end_date

```
### Relationships   
1. Many-to-One with Villa   
    - Each coupon is associated with one villa   
    - A villa can have multiple coupons (accessible via villa.coupons.all())   
2. Many-to-Many with User (applied\_users)   
    - Tracks users who have used the coupon   
3. Many-to-Many with User (targeted\_users)   
    - Specifies users for whom the coupon is intended   
   
### Usage in the System   
1. Coupon Creation: Used in the CouponViewSet to create new coupons for villas.   
2. Price Calculation: Referenced in the BookingService to apply discounts during booking price calculations.   
3. Coupon Listing: Used in the CouponViewSet to list and filter coupons based on various criteria.   
4. Coupon Application: Checked during the booking process to validate and apply coupons.   
   
### Validation   
The Coupon model employs several validation mechanisms, primarily implemented in the CouponSerializer and CouponService. Here are the specific validations used in the system:   
### CouponSerializer Validations   
1. Discount Value Validation   
   
```
def validate_discount_value(self, value):
    discount_type = self.initial_data.get("discount_type")
    if discount_type == "PERCENTAGE" and value > 100:
        raise serializers.ValidationError("Percentage discount value cannot exceed 100%.")
    return value

```
1. Applicable Days Validation   
   
```
def validate_applicable_days(self, value):
    if not any(value):
        raise ValidationError("At least one day must be set to True in applicable_days.")
    return value

```
1. Overall Data Validation   
   
```
def validate(self, data):
    CouponService.validate_date_range(data.get("usage_start_date"), data.get("usage_end_date"), date_type="Usage")
    CouponService.validate_date_range(data.get("apply_start_date"), data.get("apply_end_date"), date_type="Apply")
    CouponValidator.validate_usage_against_display(
        data.get("apply_start_date"),
        data.get("apply_end_date"),
        data.get("usage_start_date"),
        data.get("usage_end_date"),
    )
    CouponService.validate_first_time_usage(data.get("first_time_booking_only"), self.context['request'].user, self.context['view'].kwargs.get('villa_slug'))
    CouponService.check_usage_overlap(self.context['view'].kwargs.get('villa_slug'), data.get("apply_start_date"), data.get("apply_end_date"))

    targeted_user_ids = self.initial_data.get("targeted_users", [])
    if data.get("max_users") is not None and len(targeted_user_ids) > data.get("max_users"):
        raise ValidationError("The number of targeted users cannot exceed the maximum number of users allowed.")

    return data

```
### CouponService Validations   
The CouponService includes several validation methods:   
1. Date Range Validation   
2. Usage Against Display Validation   
3. First Time Usage Validation   
4. Usage Overlap Check   
   
### Additional Validation Notes   
- The system ensures that only villa owners or admin users can create coupons for a villa.   
- The unique constraint on the name field ensures that each coupon has a unique code across the entire system.   
- Date validations ensure logical consistency between apply and usage dates.   
- The usage overlap check prevents conflicts in coupon applications and ensures clear pricing for users.   
   
### Indexing (todo)   
Suggested indexes to improve query performance:   
```
class Meta:
    indexes = [
        models.Index(fields=['villa', 'usage_start_date', 'usage_end_date']),
        models.Index(fields=['discount_type']),
        models.Index(fields=['name']),
    ]

```
### Notes   
- The separation of apply dates and usage dates allows for marketing coupons in advance of their actual applicability.   
- The applicable\_days field provides flexibility in creating coupons that apply only on specific days of the week.   
- The targeted\_users field allows for creating user-specific promotions.   
- The first\_time\_booking\_only flag enables special promotions for new customers.   
