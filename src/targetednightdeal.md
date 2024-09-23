# TargetedNightDeal

TargetedNightDeal Model Documentation

### 1.1 Fields

1. `deal_type` (CharField)
   - Description: Type of the deal (Percentage discount or Flat rate)
   - Choices:
     - PERCENTAGE: "PERCENTAGE"
     - FLAT: "FLAT"
   - Default: "FLAT"
   - Max Length: 10
2. `discount_value` (DecimalField)
   - Description: The value of the discount
   - Max Digits: 10
   - Decimal Places: 2
3. `name` (CharField)
   - Description: Name of the deal
   - Max Length: 255
   - Unique: True
4. `villa` (ForeignKey to Villa)
   - Description: The villa this deal is associated with
   - Related Name: "cumulative_deals"
5. `display_start_date` (DateField)
   - Description: The date when the deal can be viewed by users
6. `display_end_date` (DateField)
   - Description: The date when the deal stops being viewed
7. `usage_start_date` (DateField)
   - Description: The date from which the deal can be used
8. `usage_end_date` (DateField)
   - Description: The final date when the deal can be used
9. `target_night` (PositiveIntegerField)
   - Description: The target night which receives the discount (e.g., 3 for the third night)

### 1.2 Methods

1. `__str__(self)`
   - Description: String representation of the TargetedNightDeal object
   - Returns: A string containing the name of the deal and the associated villa name
