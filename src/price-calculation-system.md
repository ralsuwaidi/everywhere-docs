# Price Calculation System

Price Calculation System Documentation

## Overview

The price calculation system in the Everywhere project is responsible for determining the total cost of a booking. It takes into account various factors such as base prices, custom rates, deals, and coupons.

## Components Involved

1. BookingService
2. BasePrice model
3. CustomRate model
4. Deal model
5. Coupon model
6. TargetedNightDeal model
7. DealViewSet

## Calculation Process

The main calculation is performed by the `calculate_price` method in the BookingService class. Here's a step-by-step breakdown of the process:

### 1. Retrieve Base Prices

- Fetch the BasePrice object for the villa.
- Calculate daily base prices for the booking duration.  


### 2. Apply Custom Rates

- Check for any applicable CustomRate objects for the booking period.
- If custom rates exist, they override the base prices for the specific dates.  


### 3. Apply General Deals

- Fetch all applicable Deal objects for the villa and booking period.
- Apply deal discounts to the daily prices.
- Note: Due to overlap prevention, there will be at most one general deal active for any given date.  


### 4. Apply Targeted Night Deals

- Check for any TargetedNightDeal objects applicable to the booking.
- Apply these deals to specific nights of the stay.  


### 5. Apply Coupon (if any)

- If a coupon is provided, validate and apply its discount.  


### 6. Calculate Final Price

- Sum up all the daily prices after applying all discounts and deals.  


## Detailed Steps

### Base Price Calculation

```
def calculate_base_price(start_date, end_date, base_price):
    daily_prices = []
    current_date = start_date
    while current_date < end_date:
        daily_prices.append(base_price.prices[current_date.weekday()])
        current_date += timedelta(days=1)
    return daily_prices

```

- Uses the BasePrice model's prices field, which contains prices for each day of the week.  


### Custom Rate Application

```
def apply_custom_rates(start_date, end_date, villa, daily_prices):
    applicable_custom_rates = CustomRateService.get_applicable_custom_rates(villa, start_date, end_date)
    # ... (code to apply custom rates)
    return daily_prices

```

- Overrides base prices with custom rates where applicable.  


### General Deal Application

```
def apply_general_deals(start_date, end_date, villa, daily_prices):
    deals = Deal.objects.filter(villa=villa, usage_start_date__lte=end_date, usage_end_date__gte=start_date)
    # ... (code to apply general deals)
    return daily_prices

```

- Applies percentage or flat rate discounts from general deals.  


### Targeted Night Deal Application

```
def apply_targeted_night_deals(start_date, end_date, villa, daily_prices, base_price):
    targeted_deals = TargetedNightDeal.objects.filter(
        villa=villa, usage_start_date__lte=end_date, usage_end_date__gte=start_date
    )
    # ... (code to apply targeted night deals)
    return daily_prices

```

- Applies discounts to specific nights of the stay based on the deal's target night.  


### Coupon Application

```
def apply_coupon(coupon, start_date, end_date, daily_prices):
    # ... (code to validate and apply coupon)
    return daily_prices

```

- Applies coupon discount if valid for the booking.  


## Deal Overlap Prevention

A crucial aspect of the pricing system is the prevention of overlapping deals. This is handled in the DealViewSet:

```
class DealViewSet(viewsets.ModelViewSet):
    serializer_class = DealSerializer
    queryset = Deal.objects.all()

    def perform_create(self, serializer):
        villa_slug = self.kwargs.get("villa_slug")
        villa = get_object_or_404(Villa, slug=villa_slug)

        if DealService.check_for_overlapping_deals(
            villa,
            serializer.validated_data["usage_start_date"],
            serializer.validated_data["usage_end_date"]
        ):
            raise ValidationError("An overlapping deal exists for the specified dates.")

        serializer.save(villa=villa)

```

This ensures that:

1. No two deals for the same villa can have overlapping date ranges.
2. When creating a new deal, the system checks for any existing deals that overlap with the proposed dates.
3. If an overlap is detected, the creation of the new deal is prevented, and a ValidationError is raised.

## Deal and Coupon Mutual Exclusivity

A critical aspect of the pricing system is the mutual exclusivity between deals and coupons. The system is designed to prevent the simultaneous application of both a deal and a coupon to a single booking. This rule is enforced in the BookingService:

```
@staticmethod
def validate_no_deal_and_coupon(booking: dict):
    """
    Validates that a booking does not apply both a deal and a coupon.
    """
    villa = booking["villa"]
    start_date = booking["start_date"]
    end_date = booking["end_date"]
    coupon = booking.get("coupon")

    if coupon:
        deal_exists = Deal.objects.filter(
            villa=villa, usage_start_date__lte=end_date, usage_end_date__gte=start_date
        ).exists()
        if deal_exists:
            raise ValidationError("Cannot apply both a deal and a coupon to the same booking.")

```

This validation ensures that:

1. If a coupon is applied to a booking, the system checks for the existence of any overlapping deals.
2. If both a coupon and an overlapping deal are detected, the system raises a ValidationError.
3. Owners must choose between using a coupon or benefiting from an active deal, but not both simultaneously.

### Implications

1. Simplified Pricing: This rule simplifies price calculations by ensuring only one type of discount (deal or coupon) is applied at a time.
2. Clear Choice: Owners are presented with a clear choice between using a coupon or a deal.
3. Prevent Excessive Discounts: This prevents scenarios where multiple discounts could stack, potentially leading to excessively reduced prices.

### Important Considerations

- **Mutual Exclusivity**: A booking cannot have both a deal and a coupon applied simultaneously. The system should clearly communicate this to users, with the UI dynamically updating to disable coupon input when a deal is applied and vice versa. Error messages should explain why both cannot be applied.
- **Date Ranges**: Start and end dates for all components (custom rates, deals, coupons) must be considered to ensure valid application.
- **Deal Exclusivity**: Only one general deal can be active for a villa at any given time, preventing overlaps.
- **Coupon Validation**: Coupons are checked for usage limits and applicability based on the villa and date range.
- **Minimum Prices**: The system ensures that final prices never fall below zero after applying any discounts.  


## Example Usage

```
total_price = BookingService.calculate_price(
    start_date=booking_start_date,
    end_date=booking_end_date,
    villa=selected_villa,
    coupon=applied_coupon  # Optional
)

```

This method returns the final total price for the booking after applying all applicable rates, deals, and discounts.
