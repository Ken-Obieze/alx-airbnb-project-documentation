# Airbnb Clone Backend – Requirements Specification

This document details the **technical and functional requirements** for core backend features of the Airbnb Clone project.  
The features covered include **User Authentication**, **Property Management**, and **Booking System**.

---

## 1. User Authentication

### Functional Requirements
- Allow users to **register** with email and password.
- Allow users to **log in** securely.
- Support **JWT-based authentication** for session management.
- Support **OAuth login** (e.g., Google, Facebook).
- Provide **role-based access control (RBAC)** for guests, hosts, and admins.

### API Endpoints
| Method | Endpoint          | Description               | Auth Required |
|--------|------------------|---------------------------|---------------|
| POST   | `/api/auth/signup` | Register a new user        | No            |
| POST   | `/api/auth/login`  | Authenticate user          | No            |
| GET    | `/api/auth/profile`| Get logged-in user profile | Yes           |

### Input/Output Specifications
- **Signup Request**
  ```json
  {
    "email": "user@example.com",
    "password": "securePassword123",
    "firstName": "John",
    "lastName": "Doe",
    "role": "guest"
  }
  ```
- **Signup Response**
  ```json
  {
  "id": "uuid",
  "email": "user@example.com",
  "role": "guest",
  "token": "jwt-token"
  }
  ```
### Validation Rules

* Email must be unique and valid format.
* Password must be at least 8 characters, include one uppercase, one lowercase, and one digit.
* Role must be one of: guest, host, admin.

### Performance Criteria

* Authentication response time should be < 300ms.
* Token validation should support up to 10,000 concurrent requests.

## 2. Property Management
### Functional Requirements

* Hosts can add, edit, and delete property listings.
* Guests can view and search listings by location, price, amenities, etc.
* Each property must include details: title, description, price, location, amenities, availability dates.

### API Endpoints
| Method | Endpoint | Description | Auth Required |
|:---|:---|:---|:---|
| POST | `/api/properties` | Create property listing | Host |
| GET | `/api/properties` | Retrieve all properties | No |
| GET | `/api/properties/:id` | Retrieve single property | No |
| PUT | `/api/properties/:id` | Update property listing | Host |
| DELETE | `/api/properties/:id` | Delete property listing | Host |

### Input/Output Specifications

- **Create Property Request**
 ```json
{
  "title": "Cozy Apartment in Lagos",
  "description": "A nice 2-bedroom apartment near the beach",
  "price": 120,
  "location": "Lagos, Nigeria",
  "amenities": ["Wi-Fi", "Air Conditioning"],
  "availability": ["2025-09-01", "2025-09-10"]
}
 ```

- **Create Property Response**
 ```json
{
  "id": "uuid",
  "title": "Cozy Apartment in Lagos",
  "hostId": "uuid",
  "price": 120,
  "status": "active"
}
 ```

### Validation Rules

* Title must be non-empty and ≤ 100 characters.
* Price must be a positive number.
* Location must be a valid string.
* Availability dates must follow ISO date format (YYYY-MM-DD).

### Performance Criteria

* Search results should return within 500ms.
* Support pagination for up to 50,000 property listings.

## 3. Booking System
### Functional Requirements

* Guests can book available properties for specific dates.
* Prevent double bookings (date overlap validation).
* Guests and hosts can cancel bookings.
* Track booking status: pending, confirmed, cancelled, completed.

### API Endpoints
| Method | Endpoint | Description | Auth Required |
|:---|:---|:---|:---|
| POST | `/api/bookings` | Create new booking | Guest |
| GET | `/api/bookings` | Retrieve user bookings | Yes |
| GET | `/api/bookings/:id` | Get booking details | Yes |
| PUT | `/api/bookings/:id` | Update booking (cancel) | Guest/Host |

- **Create Booking Request**
 ```json
{
  "propertyId": "uuid",
  "checkInDate": "2025-09-05",
  "checkOutDate": "2025-09-10",
  "guests": 2
}
 ```

- **Create Booking Response**
 ```json
{
  "id": "uuid",
  "propertyId": "uuid",
  "guestId": "uuid",
  "status": "pending",
  "checkInDate": "2025-09-05",
  "checkOutDate": "2025-09-10"
}
 ```

### Validation Rules

* Check-in and check-out dates must be valid and check-out > check-in.
* Guests count must not exceed property capacity.
* Prevent overlapping bookings for the same property.

### Performance Criteria

* Booking creation must validate availability within 300ms.
* System must handle 1,000 concurrent booking requests without failure.