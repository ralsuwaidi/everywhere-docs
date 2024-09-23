# UserProfile Unit Tests

UserProfile Unit Tests Documentation

## Overview

These unit tests ensure that the UserProfile model functions correctly and meets business requirements. They validate the creation, retrieval, and updating of user profiles, as well as specific business rules related to user information.

## Test Cases

### 1. Profile Creation

**Test:** `test_profile_creation`
**Business Importance:** Ensures that user profiles are correctly associated with user accounts, allowing for the storage of additional user information crucial for personalized service and communication.

```
def test_profile_creation(self, user_profile):
    assert isinstance(user_profile, UserProfile)
    assert str(user_profile) == f"Profile of {user_profile.user.email}"

```

### 2. Profile Fields

**Test:** `test_profile_fields`
**Business Importance:** Validates that all required user information (bio, phone number, nationality) is correctly stored and retrieved, ensuring accurate user data for customer service, marketing, and operational purposes.

```
def test_profile_fields(self, user_profile):
    assert user_profile.bio == "Test bio"
    assert isinstance(user_profile.phone_number, PhoneNumber)
    assert str(user_profile.phone_number) == "+1234567890"
    assert user_profile.nationality.code == "US"

```

### 3. Profile Serialization

**Test:** `test_serializer_with_valid_data`
**Business Importance:** Ensures that user profile data can be correctly serialized for API responses, maintaining data integrity in client-server communications and third-party integrations.

```
def test_serializer_with_valid_data(self):
    valid_data = {
        'bio': 'New bio',
        'phone_number': '+33987654321',
        'nationality': 'FR'
    }
    serializer = UserProfileSerializer(data=valid_data)
    assert serializer.is_valid()

```

### 4. Phone Number Validation

**Test:** `test_serializer_with_invalid_phone`
**Business Importance:** Validates phone number formats to ensure accurate contact information for customer communication, booking confirmations, and emergency notifications.

```
def test_serializer_with_invalid_phone(self):
    invalid_data = {
        'bio': 'New bio',
        'phone_number': 'invalid',
        'nationality': 'FR'
    }
    serializer = UserProfileSerializer(data=invalid_data)
    assert not serializer.is_valid()
    assert 'phone_number' in serializer.errors

```

### 5. Nationality Validation

**Test:** `test_serializer_with_invalid_nationality`
**Business Importance:** Ensures that only valid country codes are accepted, maintaining data quality for regulatory compliance, targeted marketing, and region-specific offerings.

```
def test_serializer_with_invalid_nationality(self):
    invalid_data = {
        'bio': 'New bio',
        'phone_number': '+9876543210',
        'nationality': 'INVALID'
    }
    serializer = UserProfileSerializer(data=invalid_data)
    assert not serializer.is_valid()
    assert 'nationality' in serializer.errors

```

### 6. Profile Retrieval

**Test:** `test_retrieve_profile`
**Business Importance:** Confirms that user profiles can be accurately retrieved, ensuring that customer service representatives and automated systems can access up-to-date user information.

```
def test_retrieve_profile(self, api_client, user, user_profile):
    api_client.force_authenticate(user=user)
    url = reverse('api:userprofile-me')
    response = api_client.get(url)

    assert response.status_code == status.HTTP_200_OK
    assert response.data['bio'] == user_profile.bio
    assert response.data['phone_number'] == str(user_profile.phone_number)
    assert response.data['nationality'] == user_profile.nationality.code

```

### 7. Profile Update

**Test:** `test_update_profile`
**Business Importance:** Validates that users can update their profile information, ensuring that the platform always has the most current user data for improved service delivery and communication.

```
def test_update_profile(self, api_client, user, user_profile):
    api_client.force_authenticate(user=user)
    url = reverse('api:userprofile-me')
    data = {
        'bio': 'Updated bio',
        'phone_number': '+12125552368',
        'nationality': 'FR'
    }
    response = api_client.put(url, data, format='json')
    assert response.status_code == status.HTTP_200_OK
    user_profile.refresh_from_db()
    assert user_profile.bio == 'Updated bio'
    assert str(user_profile.phone_number) == '+12125552368'
    assert user_profile.nationality.code == 'FR'

```

### 8. Partial Profile Update

**Test:** `test_partial_update_profile`
**Business Importance:** Ensures that users can update individual fields of their profile without affecting others, providing flexibility in profile management and reducing data entry requirements.

```
def test_partial_update_profile(self, api_client, user, user_profile):
    api_client.force_authenticate(user=user)
    url = reverse('api:userprofile-me')
    data = {'bio': 'Partially updated bio'}
    response = api_client.patch(url, data)
    assert response.status_code == status.HTTP_200_OK
    user_profile.refresh_from_db()
    assert user_profile.bio == 'Partially updated bio'

```

## Business Impact

These unit tests ensure that the UserProfile functionality meets critical business needs:

1. **Data Integrity:** Ensures that user information is accurately stored and retrieved, maintaining the quality of our user database.
2. **Compliance:** Validates nationality and phone number formats, supporting regulatory compliance efforts.
3. **User Experience:** Confirms that users can easily view and update their profiles, enhancing user satisfaction and engagement.
4. **Operational Efficiency:** Guarantees that customer service and marketing teams have access to accurate, up-to-date user information.
5. **Personalization:** By ensuring correct storage of user details, enables personalized services and communication.
6. **API Reliability:** Validates serialization and deserialization, ensuring reliable data exchange with front-end applications and third-party integrations.
