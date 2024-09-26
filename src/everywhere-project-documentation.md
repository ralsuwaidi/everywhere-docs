# Everywhere Project Documentation

## 1. Introduction

Everywhere is a comprehensive villa booking platform that connects villa owners with potential guests. The system facilitates the entire process from villa listing to booking and review management.

### 1.1 Key Components

1. **User Management**
   - User registration and authentication
   - User profiles with additional information
   - Role-based access (Villa Owners, Guests, Regular Users)
2. **Villa Management**
   - Villa creation and editing
   - Approval process for new villas
   - Detailed villa information including amenities and rooms
3. **Booking System**
   - Flexible booking process
   - Dynamic pricing with base prices and custom rates
   - Deal and discount application
4. **Review System**
   - Allow guests to leave reviews for villas
   - Display aggregated ratings for villas (todo)
5. **Deals and Discounts**
   - Support for various types of deals (e.g., percentage discounts, flat rates)
   - Coupon system for additional discounts
   - Targeted night deals for specific booking periods
6. **Admin Interface**
   - Villa approval management
   - User management
   - System-wide settings and configurations

### 1.2 Core Features

1. **User-Centric Design**
   - Email-based authentication (todo)
   - Detailed user profiles including nationality and phone number
   - Role-based permissions (Villa Owner, Guest, Regular User)
2. **Flexible Villa Listings**
   - Detailed villa information including amenities, rooms, and images
   - Support for multiple rooms per villa
   - Custom pricing options (base prices, custom rates for specific periods)
3. **Advanced Booking Management**
   - Real-time availability checking
   - Dynamic pricing calculation
   - Support for add-ons and special requests
4. **Comprehensive Review System**
   - Allow reviews only for past guests
   - Display aggregated ratings and individual reviews
5. **Versatile Deals and Discounts**
   - Support for various types of deals (percentage, flat rate)
   - Coupon system with usage limitations
   - Targeted night deals for specific booking durations
6. **Robust API**
   - RESTful API design
   - Detailed serializers for data transformation
   - Nested routes for related data (e.g., rooms for a specific villa)
7. **Data Integrity and Validation**
   - Extensive use of Django's model validation
   - Custom validators for complex business rules
   - Transactional operations to ensure data consistency

### 1.3 Technology Stack

- Backend: Django with Django REST Framework
- Database: PostgreSQL (inferred from the use of JSONField and ArrayField)
- Authentication: Token-based authentication (likely using Django REST Framework's token auth)
- File Storage: Likely using Django's FileField, possibly with cloud storage integration (todo)
