# User Registration   
User Registration Documentation   
## Overview   
The user registration process allows new users to create an account in the Everywhere system. It uses a custom serializer and view to handle the registration process.   
## Process   
1. The client sends a POST request to the registration endpoint with the required user data.   
2. The system validates the input data.   
3. If valid, a new user account is created.   
4. The system returns the created user data (excluding the password).   
   
## API Endpoint   
- **URL**: `/api/users/register/`   
- **Method**: POST   
- **Authentication**: Not required (AllowAny permission)   
   
## Request Payload   
The registration request should include the following fields:   
- `email` (string, required): The user's email address   
- `name` (string, required): The user's name   
- `password` (string, required): The user's chosen password   
   
Example request body:   
```
{
    "email": "newuser@example.com",
    "name": "New User",
    "password": "securepassword123"
}

```
## Response   
### Success Response   
- **Code**: 201 CREATED   
- **Content**: JSON object containing the created user's data (excluding password)   
   
Example response:   
```
{
    "email": "newuser@example.com",
    "name": "New User"
}

```
### Error Response   
- **Code**: 400 BAD REQUEST   
- **Content**: JSON object containing error details   
   
Example error response:   
```
{
    "email": [
        "This field must be unique."
    ],
    "password": [
        "This field is required."
    ]
}

```
## Implementation Details   
### Serializer   
The registration process uses a custom serializer: `UserRegistrationSerializer`   
```
class UserRegistrationSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True)
    
    class Meta:
        model = User
        fields = ['email', 'name', 'password']
    
    def create(self, validated_data):
        user = User.objects.create_user(
            email=validated_data['email'],
            name=validated_data['name'],
            password=validated_data['password']
        )
        return user

```
This serializer:   
- Ensures the password field is write-only (not included in response)   
- Uses the `create\_user` method of the custom UserManager to create the user   
   
### View   
The registration is handled by the `register` action in the `UserViewSet`:   
```
class UserViewSet(RetrieveModelMixin, ListModelMixin, UpdateModelMixin, GenericViewSet):
    # ... other methods ...

    @action(detail=False, methods=['post'], permission_classes=[permissions.AllowAny])
    def register(self, request):
        serializer = UserRegistrationSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

```
This view:   
- Allows unauthenticated access (AllowAny permission)   
- Validates the input data using the UserRegistrationSerializer   
- Creates a new user if the data is valid   
- Returns appropriate responses based on the success or failure of the registration   
   
## Validation   
- Email must be unique across all users   
- Password validation is handled by Django's default password validators (can be customized in settings)   
- Additional validation can be added in the serializer if needed   
   
## Notes   
- The registration process automatically sets the user's role to the default (ROLE\_REGULAR)   
- After registration, the user will need to log in separately to obtain an authentication token   
- Consider implementing email verification for added security (todo)   
- There's an automatic user profile creation once a user model is created   
