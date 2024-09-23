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
     - ROLE_VILLA_OWNER: "villa_owner"
     - ROLE_GUEST: "guest"
     - ROLE_REGULAR: "regular"
   - Default: ROLE_REGULAR

## Inherited Fields

As the User model extends AbstractUser, it also includes these fields from Django's built-in User model:

- `password`
- `last_login`
- `is_superuser`
- `is_staff`
- `is_active`
- `date_joined`  


## Removed Fields

The following fields from AbstractUser are explicitly set to None:

- `first_name`
- `last_name`
- `username`  


## Methods

1. `get_absolute_url(self) -> str`
   - Description: Returns the URL for the user's detail view
   - Returns: A string URL for the user detail page
2. `__str__(self)`
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

1. `_create_user(self, email: str, password: str \| None, \*\*extra_fields)`
2. `create_user(self, email: str, password: str \| None = None, \*\*extra_fields)`
3. `create_superuser(self, email: str, password: str \| None = None, \*\*extra_fields)`

These methods ensure that users are created with email as the unique identifier instead of a username.

## Authentication

- The `USERNAME_FIELD` is set to 'email', making the email address the primary identifier for authentication.
- The `REQUIRED_FIELDS` list is empty, meaning no additional fields are required during user creation beyond the email and password.  


## Notes

- This User model uses email for authentication instead of a username.
- The `name` field is used instead of separate `first_name` and `last_name` fields.
- The `role` field allows for basic role-based permissions within the application.
