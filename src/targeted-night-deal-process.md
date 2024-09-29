# Targeted Night Deal Process

Targeted Night Deal System Documentation  
Targeted Night Deals are a specialized type of promotional offer in the Everywhere system that apply discounts to specific nights of a guest's stay. Unlike regular deals or coupons, which typically apply to the entire booking, Targeted Night Deals focus on particular nights within a reservation, allowing for more nuanced and strategic discounting.  
Key differences from regular deals and coupons:

1. **Night-specific application**: Targeted Night Deals apply to a specific night (e.g., the 3rd night) rather than the entire stay.
2. **Encourages longer stays**: By offering discounts on specific nights, these deals can incentivize guests to extend their bookings.
3. **Flexible targeting**: Villa owners can create promotions like "3rd night free" or "50% off the 5th night" to suit their business strategies.

The Targeted Night Deal creation process allows villa owners or admin users to create these special promotional offers for villas within the Everywhere system. It uses a custom serializer, service, and view to handle the creation process, ensuring proper validation and association with villas.

### Business Rules for Targeted Night Deals

1. Only authenticated users (villa owners or admin users) can create targeted night deals.
2. Targeted night deals must be associated with a specific villa.
3. The deal creator must be the owner of the villa or an admin user.
4. Deal names must be unique within the system.
5. Discount values must be positive.
6. For percentage-based deals, the discount value cannot exceed `100%`.
7. Display and usage date ranges must be valid and logically consistent.
8. The usage date range must fall within the display date range.
9. The `target_night` must be a positive integer.
10. No overlapping targeted night deals are allowed for the same villa, date range, and `target_night`.
11. The system must validate all deal parameters before creation.
12. Deals can be of two types: percentage-based or flat-rate discounts.

### Process

1. The client sends a POST request to the targeted night deal creation endpoint with the required deal data.
2. The system validates the input data, including checking for overlapping deals.
3. If valid, a new targeted night deal is created and associated with the specified villa.
4. The system returns the created deal data.

### API Endpoint

- URL: `/api/villas/{villa_slug}/targeted-night-deals/`
- Method: `POST`
- Authentication: Required (`IsAuthenticated` and `IsOwnerOrAdmin` permissions)  


### Request Payload

The targeted night deal creation request should include the following fields:

- `name` (string, required): The name of the deal
- `deal_type` (string, required): The type of deal (`"PERCENTAGE"` or `"FLAT"`)
- `discount_value` (decimal, required): The value of the discount
- `display_start_date` (date, required): The start date for displaying the deal
- `display_end_date` (date, required): The end date for displaying the deal
- `usage_start_date` (date, required): The start date for using the deal
- `usage_end_date` (date, required): The end date for using the deal
- `target_night` (integer, required): The specific night of the stay to which the discount applies  


Example request body:

```
{
    "name": "Third Night Free",
    "deal_type": "PERCENTAGE",
    "discount_value": "100.00",
    "display_start_date": "2024-06-01",
    "display_end_date": "2024-08-31",
    "usage_start_date": "2024-06-01",
    "usage_end_date": "2024-08-31",
    "target_night": 3
}

```

### Response

### Success Response

- Code: `201 CREATED`
- Content: JSON object containing the created targeted night deal's data  


Example response:

```
{
    "id": 1,
    "name": "Third Night Free",
    "deal_type": "PERCENTAGE",
    "discount_value": "100.00",
    "display_start_date": "2024-06-01",
    "display_end_date": "2024-08-31",
    "usage_start_date": "2024-06-01",
    "usage_end_date": "2024-08-31",
    "target_night": 3,
    "villa": 1
}

```

### Error Response

- Code: `400 BAD REQUEST`
- Content: JSON object containing error details  


Example error response:

```
{
    "discount_value": [
        "Ensure that there are no more than 2 decimal places."
    ],
    "target_night": [
        "Ensure this value is greater than or equal to 1."
    ]
}

```

### Implementation Details

### Serializer

The targeted night deal creation process uses the `TargetedNightDealSerializer`:

```
class TargetedNightDealSerializer(serializers.ModelSerializer):
    class Meta:
        model = TargetedNightDeal
        fields = [
            "id", "name", "villa", "deal_type", "discount_value",
            "display_start_date", "display_end_date", "usage_start_date",
            "usage_end_date", "target_night"
        ]
        read_only_fields = ("villa",)

    def validate_discount_value(self, value):
        deal_type = self.initial_data.get("deal_type")
        if value <= 0:
            raise serializers.ValidationError("Discount value must be greater than zero.")
        if deal_type == "PERCENTAGE" and value > 100:
            raise serializers.ValidationError("Percentage discount value cannot exceed 100%.")
        return value

    def validate_target_night(self, value):
        if value < 1:
            raise serializers.ValidationError("The target night must be one or greater.")
        return value

    def validate(self, data):
        TargetedNightDealService.validate_targeted_night_deal_creation(data)
        return data

```

This serializer:

- Ensures all required fields are provided
- Validates the `discount_value` based on the `deal_type`
- Validates the `target_night` value
- Uses the `TargetedNightDealService` for additional validation  


### Service

The `TargetedNightDealService` handles business logic and additional validation:

```
class TargetedNightDealService:
    @staticmethod
    def validate_targeted_night_deal_creation(deal_data):
        TargetedNightDealService.validate_date_range(
            deal_data.get("display_start_date"),
            deal_data.get("display_end_date"),
            date_type="Display"
        )
        TargetedNightDealService.validate_date_range(
            deal_data.get("usage_start_date"),
            deal_data.get("usage_end_date"),
            date_type="Usage"
        )
        TargetedNightDealService.validate_usage_against_display(
            deal_data.get("display_start_date"),
            deal_data.get("display_end_date"),
            deal_data.get("usage_start_date"),
            deal_data.get("usage_end_date"),
        )

    @staticmethod
    def check_for_overlapping_deals(villa, start_date, end_date, target_night):
        return TargetedNightDeal.objects.filter(
            villa=villa,
            usage_start_date__lte=end_date,
            usage_end_date__gte=start_date,
            target_night=target_night
        ).exists()

```

### View

The targeted night deal creation is handled by the `create` method in the `TargetedNightDealViewSet`:

```
class TargetedNightDealViewSet(viewsets.ModelViewSet):
    serializer_class = TargetedNightDealSerializer
    permission_classes = [IsAuthenticated, IsOwnerOrAdmin]

    def perform_create(self, serializer):
        villa_slug = self.kwargs.get("villa_slug")
        villa = get_object_or_404(Villa, slug=villa_slug)
        if not self.request.user.is_staff and villa.owner != self.request.user:
            raise PermissionDenied("You do not have permission to create targeted night deals for this villa.")
        if TargetedNightDealService.check_for_overlapping_deals(
            villa,
            serializer.validated_data["usage_start_date"],
            serializer.validated_data["usage_end_date"],
            serializer.validated_data["target_night"]
        ):
            raise ValidationError("An overlapping targeted night deal exists for the specified dates and target night.")
        serializer.save(villa=villa)

```

This view:

- Ensures the user has permission to create targeted night deals for the villa
- Checks for overlapping deals before creation
- Associates the deal with the correct villa  


### Validation

- Deal `name` must be unique
- `discount_value` must be positive and, for percentage deals, not exceed `100%`
- Display and usage date ranges must be valid and logically consistent
- `target_night` must be a positive integer
- No overlapping targeted night deals are allowed for the same villa, date range, and `target_night`
- The user creating the deal must be the villa owner or an admin  


### Notes

- Targeted night deals are automatically associated with the villa specified in the URL
- The system prevents creation of overlapping targeted night deals for the same villa, date range, and `target_night`
- Consider implementing a maximum limit on the number of active targeted night deals per villa
- Future enhancement: Allow for combinable targeted night deals or more complex multi-night discounts
- The `target_night` field allows for specific promotions like "third night free" or "50% off the fifth night"
- These deals can be particularly effective for encouraging longer stays
