# Deal Creation Process   
Deal System Overview Documentation   
## Overview   
The deal creation process allows villa owners or admin users to create promotional offers for villas in the Everywhere system. It uses a custom serializer, service, and view to handle the creation process, ensuring proper validation and association with villas.   
## **Business Rules for Deals**   
1. Only authenticated users (villa owners or admin users) can create deals.   
2. Deals must be associated with a specific villa.   
3. The deal creator must be the owner of the villa or an admin user.   
4. Deal names must be unique within the system.   
5. Discount values must be positive.   
6. For percentage-based deals, the discount value cannot exceed 100%.   
7. Display and usage date ranges must be valid and logically consistent.   
8. The usage date range must fall within the display date range.   
9. At least one day of the week must be selected for the deal to be applicable.   
10. No overlapping deals are allowed for the same villa and date range.   
11. Deals must have a specified maximum number of nights they apply to.   
12. Deals must have a specified minimum number of nights required for eligibility.   
13. The system must validate all deal parameters before creation.   
14. Deals can be of two types: percentage-based or flat-rate discounts.   
   
## Process   
1. The client sends a POST request to the deal creation endpoint with the required deal data.   
2. The system validates the input data, including checking for overlapping deals.   
3. If valid, a new deal is created and associated with the specified villa.   
4. The system returns the created deal data.   
   
## API Endpoint   
- **URL**: /api/villas/{villa\_slug}/deals/   
- **Method**: POST   
- **Authentication**: Required (IsAuthenticated and IsOwnerOrAdmin permissions)   
   
## Request Payload   
The deal creation request should include the following fields:   
- name (string, required): The name of the deal   
- deal\_type (string, required): The type of deal ("PERCENTAGE" or "FLAT")   
- discount\_value (decimal, required): The value of the discount   
- display\_start\_date (date, required): The start date for displaying the deal   
- display\_end\_date (date, required): The end date for displaying the deal   
- usage\_start\_date (date, required): The start date for using the deal   
- usage\_end\_date (date, required): The end date for using the deal   
- max\_nights\_of\_deal (integer, required): Maximum number of nights the deal applies to   
- min\_nights (integer, required): Minimum number of nights required for the deal   
- applicable\_days (array of boolean, required): 7 boolean values indicating applicability for each day of the week   
   
Example request body:   
```
{
    "name": "Summer Special",
    "deal_type": "PERCENTAGE",
    "discount_value": "15.00",
    "display_start_date": "2024-06-01",
    "display_end_date": "2024-08-31",
    "usage_start_date": "2024-06-01",
    "usage_end_date": "2024-08-31",
    "max_nights_of_deal": 7,
    "min_nights": 3,
    "applicable_days": [true, true, true, true, true, false, false]
}

```
## Response   
### Success Response   
- **Code**: 201 CREATED   
- **Content**: JSON object containing the created deal's data   
   
Example response:   
```
{
    "id": 1,
    "name": "Summer Special",
    "deal_type": "PERCENTAGE",
    "discount_value": "15.00",
    "display_start_date": "2024-06-01",
    "display_end_date": "2024-08-31",
    "usage_start_date": "2024-06-01",
    "usage_end_date": "2024-08-31",
    "max_nights_of_deal": 7,
    "min_nights": 3,
    "applicable_days": [true, true, true, true, true, false, false],
    "villa": 1
}

```
### Error Response   
- **Code**: 400 BAD REQUEST   
- **Content**: JSON object containing error details   
   
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
## Implementation Details   
### Serializer   
The deal creation process uses the DealSerializer:   
```
class DealSerializer(serializers.ModelSerializer):
    class Meta:
        model = Deal
        fields = [
            "id", "villa", "name", "deal_type", "discount_value",
            "display_start_date", "display_end_date", "usage_start_date",
            "usage_end_date", "max_nights_of_deal", "min_nights",
            "applicable_days"
        ]
        read_only_fields = ("villa",)

    def validate_discount_value(self, value):
        deal_type = self.initial_data.get("deal_type")
        if value <= 0:
            raise serializers.ValidationError("Discount value must be greater than zero.")
        if deal_type == "PERCENTAGE" and value > 100:
            raise serializers.ValidationError("Percentage discount value cannot exceed 100%.")
        return value

    def validate(self, data):
        DealService.validate_deal_creation(data)
        return data

```
This serializer:   
- Ensures all required fields are provided   
- Validates the discount value based on the deal type   
- Uses the DealService for additional validation   
   
### Service   
The DealService handles business logic and additional validation:   
```
class DealService:
    @staticmethod
    def validate_deal_creation(deal_data):
        DealService.validate_date_range(
            deal_data.get("display_start_date"),
            deal_data.get("display_end_date"),
            date_type="Display"
        )
        DealService.validate_date_range(
            deal_data.get("usage_start_date"),
            deal_data.get("usage_end_date"),
            date_type="Usage"
        )
        DealService.validate_usage_against_display(
            deal_data.get("display_start_date"),
            deal_data.get("display_end_date"),
            deal_data.get("usage_start_date"),
            deal_data.get("usage_end_date"),
        )
        if not any(deal_data.get("applicable_days", [])):
            raise ValidationError("At least one day must be selected for the deal to be applicable.")

```
### View   
The deal creation is handled by the create method in the DealViewSet:   
```
class DealViewSet(viewsets.ModelViewSet):
    serializer_class = DealSerializer
    permission_classes = [IsAuthenticated, IsOwnerOrAdmin]

    def perform_create(self, serializer):
        villa_slug = self.kwargs.get("villa_slug")
        villa = get_object_or_404(Villa, slug=villa_slug)
        if not self.request.user.is_staff and villa.owner != self.request.user:
            raise PermissionDenied("You do not have permission to create deals for this villa.")
        if DealService.check_for_overlapping_deals(
            villa,
            serializer.validated_data["usage_start_date"],
            serializer.validated_data["usage_end_date"]
        ):
            raise ValidationError("An overlapping deal exists for the specified dates.")
        serializer.save(villa=villa)

```
This view:   
- Ensures the user has permission to create deals for the villa   
- Checks for overlapping deals before creation   
- Associates the deal with the correct villa   
   
## Validation   
- Deal name must be unique   
- Discount value must be positive and, for percentage deals, not exceed 100%   
- Display and usage date ranges must be valid and logically consistent   
- At least one day must be selected in applicable\_days   
- No overlapping deals are allowed for the same villa and date range   
- The user creating the deal must be the villa owner or an admin   
   
## Notes   
- Deals are automatically associated with the villa specified in the URL   
- The system prevents creation of overlapping deals for the same villa   
- Consider implementing a maximum limit on the number of active deals per villa   
- Future enhancement: Allow for combinable deals or tiered discounts   
