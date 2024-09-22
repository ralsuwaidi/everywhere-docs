# Coupon   
Coupon Model Documentation   
### 1.1 Fields   
1. `name` (CharField)   
    - Description: The name of the coupon   
    - Max Length: 255 characters   
    - Unique: True   
2. `villa` (ForeignKey to Villa)   
    - Description: The villa this coupon is associated with   
    - On Delete: CASCADE   
3. `apply\_start\_date` (DateField)   
    - Description: The first date the coupon can be applied   
4. `apply\_end\_date` (DateField)   
    - Description: The final date the coupon can be applied   
5. `usage\_start\_date` (DateField)   
    - Description: The first date the coupon can be used   
6. `usage\_end\_date` (DateField)   
    - Description: The final date the coupon can be used   
7. `max\_users` (PositiveIntegerField)   
    - Description: The maximum number of users who can use the coupon   
8. `applied\_users` (ManyToManyField to User)   
    - Description: Users that have used this coupon   
    - Related Name: "used\_coupons"   
    - Blank: True   
9. `targeted\_users` (ManyToManyField to User)   
    - Description: Users that this coupon is specifically meant for   
    - Related Name: "targeted\_coupons"   
    - Blank: True   
10. `discount\_type` (CharField)   
    - Description: The type of discount (flat rate or percentage)   
    - Max Length: 10   
    - Choices: ("FLAT", "Flat Rate"), ("PERCENTAGE", "Percentage")   
    - Default: "FLAT"   
11. `discount\_value` (DecimalField)   
    - Description: The value of the discount   
    - Max Digits: 8   
    - Decimal Places: 2   
12. `min\_days` (PositiveIntegerField)   
    - Description: The minimum days to qualify for the coupon   
13. `max\_days` (PositiveIntegerField)   
    - Description: The maximum days the coupon can be applied   
14. `first\_time\_booking\_only` (BooleanField)   
    - Description: Whether the coupon is only for first-time bookings   
    - Default: False   
15. `applicable\_days` (ArrayField of BooleanField)   
    - Description: Array of booleans representing if the coupon applies from Monday to Sunday   
    - Size: 7   
   
### 1.2 Methods   
1. `is\_active` (property)   
    - Description: Checks if the coupon is active based on the current date   
    - Returns: Boolean   
2. `\_\_str\_\_(self)`   
    - Description: String representation of the Coupon object   
    - Returns: A string with the coupon name and associated villa name   
