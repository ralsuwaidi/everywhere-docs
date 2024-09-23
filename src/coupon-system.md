# Coupon System

Coupon System Documentation

## 1. Coupon Model

[Coupon](coupon.md)

## 2. Coupon Creation and Management

### 2.1 Creating a Coupon

Coupons are created through the `CouponSerializer` and managed by the `CouponService`. The creation process includes:

1. Validating coupon data:
   - Ensuring usage dates are in the future
   - Checking for logical date ranges (usage dates within apply dates)
   - Validating discount values (percentage not exceeding 100%)
   - Ensuring at least one day is selected for applicability
2. Checking for overlapping coupons for the same villa
3. Validating first-time booking restrictions if applicable

### 2.2 Retrieving Coupons

The `CouponService` provides methods to retrieve coupons:

1. `get_filtered_coupons(user, villa_slug=None)`:
   - Retrieves coupons based on user permissions and villa association
   - Staff users can see all coupons
   - Villa owners can see coupons for their villas
   - Regular users can see coupons they've used or are targeted for

## 3. Coupon Validation

The `CouponService` includes several validation methods:

1. `validate_coupons_for_villa(coupons, villa_id)`:
   - Ensures coupons are applicable to the specified villa
2. `validate_first_time_usage(first_time_booking_only, user_id, villa_slug)`:
   - Checks if a user is eligible for a first-time booking coupon
3. `check_usage_overlap(villa_slug, start_date, end_date)`:
   - Prevents creation of overlapping coupons for the same villa
4. `check_coupon_applicability(coupon, start_date, end_date)`:
   - Verifies if a coupon is applicable for given booking dates
5. `check_coupon_usage_limit(coupon)`:
   - Ensures the coupon hasn't exceeded its usage limit

## 4. Applying Coupons to Bookings

The `apply_coupon` method in `CouponService` handles the application of coupons to bookings:

1. Checks coupon usage limit and applicability
2. Applies the discount to each applicable day of the booking
3. Ensures the discounted price doesn't go below zero

The discount calculation is handled by the `calculate_discount` function in `price_helpers`:

- For percentage discounts: `discount = price \* (discount_value / 100)`
- For flat rate discounts: `discount = min(discount_value, price)`  


## 5. API Endpoints

1. `POST /api/coupons/`: Create a new coupon (staff or villa owner only)
2. `GET /api/coupons/`: List coupons (filtered based on user permissions)
3. `GET /api/coupons/{id}/`: Retrieve a specific coupon
4. `PUT /api/coupons/{id}/`: Update a coupon (staff or villa owner only)
5. `DELETE /api/coupons/{id}/`: Delete a coupon (staff or villa owner only)

## 6. Integration with Booking System

- When creating a booking, the `BookingSerializer` accepts a coupon name.
- The `BookingService.calculate_price` method applies the coupon discount if a valid coupon is provided.
- The booking system ensures that a coupon and a deal are not applied simultaneously to the same booking.  


## 7. Security Considerations

- Coupon creation and management are restricted to staff users and villa owners.
- Coupon usage is tracked to prevent exceeding the maximum usage limit.
- The system validates that users can only use coupons they're eligible for (e.g., targeted coupons, first-time booking coupons).
