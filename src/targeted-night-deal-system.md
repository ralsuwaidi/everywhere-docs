# Targeted Night Deal System   
Targeted Night Deal System Documentation   
## 1. Targeted Night Deal Model   
[TargetedNightDeal](targetednightdeal.md)    
## 2. TargetedNightDeal Serializer   
The `TargetedNightDealSerializer` handles the serialization and deserialization of TargetedNightDeal objects. It includes the following validations:   
1. Ensures the discount value is appropriate based on the deal type:   
    - For percentage deals: between 0% and 100%   
    - For flat rate deals: greater than zero   
2. Validates that the target night is one or greater   
3. Checks for logical inconsistencies in date ranges:   
    - Display start date must be before display end date   
    - Usage start date must be before usage end date   
    - Usage dates must fall within display dates   
   
## 3. TargetedNightDeal Service   
The `TargetedNightDealService` provides the following functionalities:   
1. `get\_deals\_for\_villa(villa\_slug)`:   
    - Retrieves all TargetedNightDeals associated with a specific villa   
2. `create\_and\_validate\_deal(villa\_slug, deal\_data)`:   
    - Creates a new TargetedNightDeal   
    - Ensures there are no overlapping deals for the specified dates   
3. `check\_for\_overlapping\_deals(villa, start\_date, end\_date)`:   
    - Checks for any overlapping deals (including regular deals) within the given date ranges for a specific villa   
   
## 4. Application of Targeted Night Deals   
Targeted Night Deals are applied in the `BookingService.apply\_targeted\_night\_deals` method:   
1. Retrieves all applicable targeted deals for the booking period   
2. For each deal:   
    - Checks if the booking duration is long enough to qualify for the deal   
    - Applies the discount to the specific targeted night   
    - Ensures the discounted price doesn't go below zero   
   
## 5. Integration with Booking System   
1. When a booking is created or updated, the system checks for applicable Targeted Night Deals   
2. The deals are applied after base prices and general deals, but before coupons   
3. Multiple Targeted Night Deals can be applied to a single booking if they target different nights   
   
## 6. API Endpoints   
1. `GET /api/villas/{villa\_slug}/targeted-night-deals/`: List all targeted night deals for a specific villa   
2. `POST /api/villas/{villa\_slug}/targeted-night-deals/`: Create a new targeted night deal for a villa   
3. `GET /api/villas/{villa\_slug}/targeted-night-deals/{id}/`: Retrieve a specific targeted night deal   
4. `PUT /api/villas/{villa\_slug}/targeted-night-deals/{id}/`: Update a targeted night deal   
5. `DELETE /api/villas/{villa\_slug}/targeted-night-deals/{id}/`: Delete a targeted night deal   
   
Note: Access to these endpoints is controlled by permissions, typically restricted to villa owners and staff members.   
## 7. Key Considerations   
1. Targeted Night Deals allow for more flexible and specific discounting strategies compared to general deals   
2. They can be used to incentivize longer stays by offering discounts on specific nights (e.g., "Stay 7 nights, get the 7th night free")   
3. The system ensures that Targeted Night Deals don't conflict with other pricing rules or each other   
4. When creating or updating these deals, the system checks for overlaps to prevent conflicting discounts   
   
## 8. Future Enhancements   
Potential areas for expansion of the Targeted Night Deal system could include:   
1. Support for recurring deals (e.g., every 3rd night is discounted)   
2. Integration with a notification system to alert users of applicable deals during the booking process   
3. Analytics to track the effectiveness of different Targeted Night Deals   
