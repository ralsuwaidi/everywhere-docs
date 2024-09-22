# Price Calculation System    
Price Calculation System Documentation   
## Overview   
The price calculation system in the Everywhere project is responsible for determining the total cost of a booking. It takes into account various factors such as base prices, custom rates, deals, and coupons.   
## Components Involved   
1. `BookingService`   
2. `BasePrice` model   
3. `CustomRate` model   
4. `Deal` model   
5. `Coupon` model   
6. `TargetedNightDeal` model   
   
## Calculation Process   
The main calculation is performed by the `calculate\_price` method in the `BookingService` class. Here's a step-by-step breakdown of the process:   
### 1. Retrieve Base Prices   
- Fetch the `BasePrice` object for the villa.   
- Calculate daily base prices for the booking duration.   
   
### 2. Apply Custom Rates   
- Check for any applicable `CustomRate` objects for the booking period.   
- If custom rates exist, they override the base prices for the specific dates.   
   
### 3. Apply General Deals   
- Fetch all applicable `Deal` objects for the villa and booking period.   
- Apply deal discounts to the daily prices.   
   
### 4. Apply Targeted Night Deals   
- Check for any `TargetedNightDeal` objects applicable to the booking.   
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
- Uses the `BasePrice` model's `prices` field, which contains prices for each day of the week.   
   
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
   
## Important Considerations   
1. **Date Ranges**: All components (custom rates, deals, coupons) have start and end dates that must be considered.   
2. **Overlapping Deals**: The system applies multiple deals if they overlap, potentially leading to significant discounts.   
3. **Coupon Validation**: Coupons are checked for validity, including usage limits and applicability to the specific villa and date range.   
4. **Minimum Prices**: The system ensures that prices don't go below zero after applying discounts.   
   
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
## Potential Improvements   
1. Implement a caching mechanism for frequently accessed price data.   
2. Add support for seasonal pricing variations.   
3. Introduce a maximum discount limit to prevent excessive price reductions.   
