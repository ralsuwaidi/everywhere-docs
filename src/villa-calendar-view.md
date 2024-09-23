# Villa Calendar View

Villa Calendar Response Documentation

## Overview

The Villa Calendar View provides a way to retrieve calendar data for a specific villa, including pricing and availability information for a given date range.

## API Endpoint

```
GET /api/villas/{slug}/calendar/

```

### Parameters

- `slug` (path parameter): The unique slug identifier for the villa
- `start_date` (query parameter): The start date for the calendar data (format: YYYY-MM-DD)
- `end_date` (query parameter): The end date for the calendar data (format: YYYY-MM-DD)  


### Response

The endpoint returns a JSON array of objects, each representing a day in the requested range:

```
[
  {
    "date": "2024-01-01",
    "price": 200.00
  },
  {
    "date": "2024-01-02",
    "price": 220.00
  },
  // ... more dates
]

```

## Implementation Details

The calendar view functionality is implemented in the `VillaViewSet` class within the `villa_view_set.py` file. It uses the `VillaService` to generate the calendar data.

### Key Components

1. **VillaViewSet**
   - Contains the `get_calendar` action method that handles the API request.
2. **VillaService**
   - Includes the `get_calendar_data` method that generates the calendar information.
   - Applies base prices, deals, and custom rates to calculate the final price for each day.
3. **BasePrice Model**
   - Provides the base pricing information for each day of the week.
4. **Deal Model**
   - Applied to adjust prices based on active deals for the villa.

### Process Flow

1. The API receives a GET request with the villa slug and date range.
2. The `get_calendar` method in `VillaViewSet` is called.
3. It retrieves the villa object and validates the input dates.
4. The `VillaService.get_calendar_data` method is called with the villa and date range.
5. This method:
   - Retrieves the base price for the villa
   - Fetches applicable deals
   - Iterates through each day in the range:
     - Applies the base price
     - Applies any applicable deals
     - Calculates the final price for the day
6. The generated calendar data is returned as a JSON response.

## Usage Notes

- Ensure that the `start_date` is before the `end_date`.
- The date range should not exceed any limits set by your application (if any).
- Prices returned include any applicable deals but do not account for custom rates or targeted night deals.  


## Error Handling

- Returns a 404 error if the villa is not found.
- Returns a 400 error for invalid date formats or ranges.  


## Future Enhancements

- Include availability information (booked/available) for each date. (todo)
- Add support for custom rates in the calendar view. (todo)
