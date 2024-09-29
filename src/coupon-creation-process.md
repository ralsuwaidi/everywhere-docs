# Coupon Creation Process   
Coupon System Documentation   
The coupon creation process allows villa owners or admin users to create promotional codes for villas in the Everywhere system. It uses a custom serializer, service, and view to handle the creation process, ensuring proper validation and association with villas.   
### Business Rules for Coupons   
1. Only authenticated users (villa owners or admin users) can create coupons.   
2. Coupons must be associated with a specific villa.   
3. The coupon creator must be the owner of the villa or an admin user.   
4. Coupon names (codes) must be unique within the system.   
5. Discount values must be positive.   
6. For percentage-based coupons, the discount value cannot exceed 100%.   
7. Apply and usage date ranges must be valid and logically consistent.   
8. The usage date range must fall within the apply date range.   
9. At least one day of the week must be selected for the coupon to be applicable.   
10. No overlapping coupons are allowed for the same villa and date range.   
11. Coupons must have a specified maximum number of users.   
12. Coupons must have specified minimum and maximum days for eligibility.   
13. The system must validate all coupon parameters before creation.   
14. Coupons can be of two types: percentage-based or flat-rate discounts.   
15. Coupons can be targeted to specific users or be available to all users.   
16. Coupons can be set for first-time bookings only.   
   
### Process   
1. The client sends a POST request to the coupon creation endpoint with the required coupon data.   
2. The system validates the input data, including checking for overlapping coupons.   
3. If valid, a new coupon is created and associated with the specified villa.   
4. The system returns the created coupon data.   
   
### API Endpoint   
- URL: `/api/villas/{villa\_slug}/coupons/`   
- Method: POST   
- Authentication: Required (IsAuthenticated and IsOwnerOrAdmin permissions)   
   
### Request Payload   
The coupon creation request should include the following fields:   
- `name` (string, required): The name or code of the coupon   
- `apply\_start\_date` (date, required): The start date for applying the coupon   
- `apply\_end\_date` (date, required): The end date for applying the coupon   
- `usage\_start\_date` (date, required): The start date for using the coupon   
- `usage\_end\_date` (date, required): The end date for using the coupon   
- `max\_users` (integer, required): Maximum number of users who can use the coupon   
- `discount\_type` (string, required): The type of discount ("PERCENTAGE" or "FLAT")   
- `discount\_value` (decimal, required): The value of the discount   
- `min\_days` (integer, required): Minimum number of days required for the coupon   
- `max\_days` (integer, required): Maximum number of days the coupon applies to   
- `first\_time\_booking\_only` (boolean, optional): Whether the coupon is for first-time bookings only   
- `applicable\_days` (array of boolean, required): 7 boolean values indicating applicability for each day of the week   
- `targeted\_users` (array of integers, optional): User IDs for targeted users   
   
Example request body:   
```
{
    "name": "SUMMER2024",
    "apply_start_date": "2024-05-01",
    "apply_end_date": "2024-08-31",
    "usage_start_date": "2024-06-01",
    "usage_end_date": "2024-08-31",
    "max_users": 100,
    "discount_type": "PERCENTAGE",
    "discount_value": "15.00",
    "min_days": 3,
    "max_days": 7,
    "first_time_booking_only": false,
    "applicable_days": [true, true, true, true, true, false, false],
    "targeted_users": [1, 2, 3]
}

```
### Response   
### Success Response   
- Code: `201` CREATED   
- Content: JSON object containing the created coupon's data   
   
Example response:   
```
{
    "id": 1,
    "name": "SUMMER2024",
    "apply_start_date": "2024-05-01",
    "apply_end_date": "2024-08-31",
    "usage_start_date": "2024-06-01",
    "usage_end_date": "2024-08-31",
    "max_users": 100,
    "discount_type": "PERCENTAGE",
    "discount_value": "15.00",
    "min_days": 3,
    "max_days": 7,
    "first_time_booking_only": false,
    "applicable_days": [true, true, true, true, true, false, false],
    "targeted_users": [1, 2, 3],
    "villa": 1
}

```
### Error Response   
- Code: `400` BAD REQUEST   
- Content: JSON object containing error details   
   
Example error response:   
```
{
    "discount_value": [
        "Ensure that there are no more than 2 decimal places."
    ],
    "applicable_days": [
        "Ensure this field has no more than 7 elements."
    ]
}

```
### Implementation Details   
### Serializer   
The coupon creation process uses the CouponSerializer:   
```
class CouponSerializer(serializers.ModelSerializer):
    class Meta:
        model = Coupon
        fields = [
            "id", "villa", "name", "apply_start_date", "apply_end_date",
            "usage_start_date", "usage_end_date", "max_users", "discount_type",
            "discount_value", "min_days", "max_days", "first_time_booking_only",
            "applicable_days", "targeted_users"
        ]
        read_only_fields = ("villa",)

    def validate_discount_value(self, value):
        discount_type = self.initial_data.get("discount_type")
        if value <= 0:
            raise serializers.ValidationError("Discount value must be greater than zero.")
        if discount_type == "PERCENTAGE" and value > 100:
            raise serializers.ValidationError("Percentage discount value cannot exceed 100%.")
        return value

    def validate(self, data):
        CouponService.validate_coupon_creation(data)
        return data

```
This serializer:   
- Ensures all required fields are provided   
- Validates the discount value based on the discount type   
- Uses the CouponService for additional validation   
   
### Service   
The CouponService handles business logic and additional validation:   
```
class CouponService:
    @staticmethod
    def validate_coupon_creation(coupon_data):
        CouponService.validate_date_range(
            coupon_data.get("apply_start_date"),
            coupon_data.get("apply_end_date"),
            date_type="Apply"
        )
        CouponService.validate_date_range(
            coupon_data.get("usage_start_date"),
            coupon_data.get("usage_end_date"),
            date_type="Usage"
        )
        CouponService.validate_usage_against_apply(
            coupon_data.get("apply_start_date"),
            coupon_data.get("apply_end_date"),
            coupon_data.get("usage_start_date"),
            coupon_data.get("usage_end_date"),
        )
        if not any(coupon_data.get("applicable_days", [])):
            raise ValidationError("At least one day must be selected for the coupon to be applicable.")
        
        CouponService.validate_max_users(
            coupon_data.get("max_users"),
            coupon_data.get("targeted_users", [])
        )

```
### View   
The coupon creation is handled by the create method in the CouponViewSet:   
```
class CouponViewSet(viewsets.ModelViewSet):
    serializer_class = CouponSerializer
    permission_classes = [IsAuthenticated, IsOwnerOrAdmin]

    def perform_create(self, serializer):
        villa_slug = self.kwargs.get("villa_slug")
        villa = get_object_or_404(Villa, slug=villa_slug)
        if not self.request.user.is_staff and villa.owner != self.request.user:
            raise PermissionDenied("You do not have permission to create coupons for this villa.")
        if CouponService.check_for_overlapping_coupons(
            villa,
            serializer.validated_data["apply_start_date"],
            serializer.validated_data["apply_end_date"]
        ):
            raise ValidationError("An overlapping coupon exists for the specified dates.")
        serializer.save(villa=villa)

```
This view:   
- Ensures the user has permission to create coupons for the villa   
- Checks for overlapping coupons before creation   
- Associates the coupon with the correct villa   
   
### Validation   
- Coupon name must be unique   
- Discount value must be positive and, for percentage coupons, not exceed 100%   
- Apply and usage date ranges must be valid and logically consistent   
- At least one day must be selected in applicable\_days   
- No overlapping coupons are allowed for the same villa and date range   
- The user creating the coupon must be the villa owner or an admin   
- The number of targeted users cannot exceed the maximum number of users   
   
### Notes   
- Coupons are automatically associated with the villa specified in the URL   
- The system prevents creation of overlapping coupons for the same villa   
- Consider implementing a maximum limit on the number of active coupons per villa   
- Future enhancement: Allow for combinable coupons or tiered discounts   
- The first\_time\_booking\_only flag allows for special promotions for new customers   
- Targeted users feature enables creation of user-specific promotions   
