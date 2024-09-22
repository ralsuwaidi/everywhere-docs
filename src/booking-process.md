# Booking Process   
Booking Process Documentation   
## Overview   
The booking process allows users to reserve a villa for a specific date range. It involves creating a Booking object, calculating the price, applying deals or coupons if applicable, and validating the booking details.   
## Steps in the Booking Process   
1. **Availability Check**   
2. **Price Calculation**   
3. **Booking Creation**   
4. **Validation**   
5. **Confirmation**   
   
## Detailed Process   
### 1. Availability Check   
Before creating a booking, the system checks if the villa is available for the requested date range.   
**API Endpoint:**   
```
GET /api/villas/{slug}/check_availability/

```
**Query Parameters:**   
- `start\_date`: The check-in date (format: YYYY-MM-DD)   
- `end\_date`: The check-out date (format: YYYY-MM-DD)   
   
**Response:**   
```
{
    "available": true/false
}

```
### 2. Price Calculation   
The system calculates the total price for the booking based on the villa's base price, any applicable custom rates, deals, or coupons.   
**API Endpoint:**   
```
GET /api/villas/{slug}/price/

```
**Query Parameters:**   
- `start\_date`: The check-in date (format: YYYY-MM-DD)   
- `end\_date`: The check-out date (format: YYYY-MM-DD)   
   
**Response:**   
```
{
    "price": 1000.00
}

```
### 3. Booking Creation   
Once availability is confirmed and price is calculated, a booking can be created.   
**API Endpoint:**   
```
POST /api/bookings/

```
**Request Body:**   
```
{
    "villa": "villa-slug",
    "start_date": "2024-07-01",
    "end_date": "2024-07-07",
    "guest_count": 4,
    "add_ons": [1, 2],  // Optional: IDs of selected add-ons
    "coupon": "SUMMER2024",  // Optional: Coupon code
    "deals": [1, 3]  // Optional: IDs of applicable deals
}

```
### 4. Validation   
During the booking creation process, several validations are performed:   
a) **Date Validation:**   
- End date must be after start date   
- Booking must be for future dates   
   
b) **Villa Approval Status:**   
- Villa must be in an approved status   
   
c) **Advance Notice:**   
- Booking must respect the villa's required advance notice period   
   
d) **Deal and Coupon Validation:**   
- Cannot apply both a deal and a coupon to the same booking   
- Coupon must be valid for the booking dates and not exceed usage limit   
   
e) **Villa Availability:**   
- No overlapping bookings allowed   
   
### 5. Confirmation   
If all validations pass, the booking is created and confirmed.   
**Response:**   
```
{
    "id": 1,
    "villa": "villa-slug",
    "start_date": "2024-07-01",
    "end_date": "2024-07-07",
    "guest_count": 4,
    "total_price": 1000.00,
    "status": "PENDING",
    "add_ons": [1, 2],
    "coupon": "SUMMER2024",
    "deals": [1, 3]
}

```
## Price Calculation Details   
The `BookingService.calculate\_price()` method handles the complex price calculation:   
1. Calculates daily base prices   
2. Applies custom rates if applicable   
3. Applies general deals   
4. Applies targeted night deals   
5. Applies coupon if any   
   
## Booking Transaction   
After a booking is confirmed, a `BookingTransaction` is created to store a snapshot of the booking details, including:   
- Villa details   
- Selected add-ons   
- Applied coupons and deals   
- Total price   
- Transaction date   
   
This provides a historical record of the booking at the time it was made.   
## Notes   
- The booking status starts as "PENDING" and can be updated to "PAID" or "CANCELLED".   
- Bookings can be retrieved, updated, or cancelled through additional API endpoints.   
- The system prevents double bookings by checking availability before confirming a booking.   
- Price calculation takes into account various factors like base price, custom rates, deals, and coupons to provide accurate pricing.   
