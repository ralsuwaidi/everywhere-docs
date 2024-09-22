# User   
User Model Documentation   
## Fields   
The User model extends Django's AbstractUser and includes the following fields:   
1. `name` (CharField)   
    - Description: The name of the user   
    - Max Length: 255 characters   
    - Blank: True (optional)   
2. `email` (EmailField)   
    - Description: The email address of the user   
    - Unique: True (must be unique across all users)   
3. `role` (CharField)   
    - Description: The role of the user   
    - Max Length: 20 characters   
    - Choices:   
        - ROLE\_VILLA\_OWNER: "villa\_owner"   
        - ROLE\_GUEST: "guest"   
        - ROLE\_REGULAR: "regular"   
    - Default: ROLE\_REGULAR   
   
## Inherited Fields   
As the User model extends AbstractUser, it also includes these fields from Django's built-in User model:   
- `password`   
- `last\_login`   
- `is\_superuser`   
- `is\_staff`   
- `is\_active`   
- `date\_joined`   
   
## Removed Fields   
The following fields from AbstractUser are explicitly set to None:   
- `first\_name`   
- `last\_name`   
- `username`   
   
## Methods   
1. `get\_absolute\_url(self) -> str`   
    - Description: Returns the URL for the user's detail view   
    - Returns: A string URL for the user detail page   
2. `\_\_str\_\_(self)`   
    - Description: String representation of the User object   
    - Returns: The user's email address   
   
## Relationships   
1. OneToOne relationship with UserProfile   
    - Related Name: 'profile'   
    - This allows access to the user's profile data via `user.profile`   
2. Reverse relationships (these are defined in other models but relate to the User model):   
    - Bookings: One-to-Many (a user can have multiple bookings)   
    - Reviews: One-to-Many (a user can write multiple reviews)   
    - Villas: One-to-Many (a user can own multiple villas)   
   
## Manager   
The User model uses a custom manager: `UserManager`   
This manager overrides the following methods:   
1. `\_create\_user(self, email: str, password: str \| None, \*\*extra\_fields)`   
2. `create\_user(self, email: str, password: str \| None = None, \*\*extra\_fields)`   
3. `create\_superuser(self, email: str, password: str \| None = None, \*\*extra\_fields)`   
   
These methods ensure that users are created with email as the unique identifier instead of a username.   
## Authentication   
- The `USERNAME\_FIELD` is set to 'email', making the email address the primary identifier for authentication.   
- The `REQUIRED\_FIELDS` list is empty, meaning no additional fields are required during user creation beyond the email and password.   
   
## Notes   
- This User model uses email for authentication instead of a username.   
- The `name` field is used instead of separate `first\_name` and `last\_name` fields.   
- The `role` field allows for basic role-based permissions within the application.   
