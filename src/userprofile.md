# UserProfile   
UserProfile Model Documentation   
## Fields   
1. `user` (OneToOneField)   
    - Description: Link to the User model   
    - Related Model: settings.AUTH\_USER\_MODEL   
    - On Delete: CASCADE (if the User is deleted, the profile is also deleted)   
    - Primary Key: True (this field serves as the primary key for the UserProfile model)   
    - Related Name: 'profile'   
2. `bio` (TextField)   
    - Description: Biography or description of the user   
    - Verbose Name: "Biography"   
    - Blank: True (optional)   
3. `phone\_number` (PhoneNumberField)   
    - Description: Contact phone number of the user   
    - Verbose Name: "Phone Number"   
    - Blank: True (optional)   
    - Note: Uses the PhoneNumberField from django-phonenumber-field for proper phone number handling and validation   
4. `nationality` (CountryField)   
    - Description: The user's nationality   
    - Verbose Name: "Nationality"   
    - Blank: True (optional)   
    - Note: Uses the CountryField from django-countries for proper country handling and validation   
   
## Methods   
1. `\_\_str\_\_(self)`   
    - Description: String representation of the UserProfile object   
    - Returns: A string in the format "Profile of {user's email}"   
   
## Relationships   
1. OneToOne relationship with User model   
    - This is the primary relationship, linking each UserProfile to exactly one User   
    - The `user` field in UserProfile is the link to the User model   
    - This allows access to the user's data via `userprofile.user`   
   
## Notes   
- The UserProfile model extends the built-in User model by providing additional fields for user information.   
- It uses specialized fields (PhoneNumberField and CountryField) for better data handling and validation of phone numbers and countries.   
- The profile is designed to be created and deleted along with the User model due to the CASCADE deletion behavior.   
   
## Usage in Views   
The UserProfile model is used in the UserProfileViewSet (user\_profile\_view.py):   
1. Retrieval:   
    - Users can retrieve their own profile using the 'me' action.   
    - The view ensures that a user can only access their own profile.   
2. Update:   
    - Users can update their profile information using PUT or PATCH requests to the 'me' endpoint.   
3. Specific Field Retrieval:   
    - There are separate actions to get the nationality and phone number individually.   
   
## Serialization   
The UserProfile model is serialized using the UserProfileSerializer (user\_profile\_serializer.py):   
- It includes fields for 'bio', 'phone\_number', and 'nationality'.   
- The serializer uses CountrySerializerField for the nationality field and PhoneNumberField for the phone\_number field, ensuring proper handling of these special field types.   
   
## API Endpoints   
1. GET `/api/profiles/me/`   
    - Retrieves the current user's profile   
2. PUT/PATCH `/api/profiles/me/`   
    - Updates the current user's profile   
3. GET `/api/profiles/get-nationality/`   
    - Retrieves just the nationality of the current user   
4. GET `/api/profiles/get-phone-number/`   
    - Retrieves just the phone number of the current user   
   
## Security Considerations   
- Access to profile information is restricted to the authenticated user who owns the profile.   
- The view layer ensures that users can only view and edit their own profiles.   
