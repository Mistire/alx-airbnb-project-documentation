# Airbnb Clone Backend - Feature Requirements Documentation

## Objective
This document outlines the technical and functional requirements for three core features of the Airbnb Clone backend: **User Authentication**, **Property Management**, and **Booking System**.


## 1. User Authentication

### Functional Requirements
- Users must be able to register using their email and password.
- JWT (JSON Web Token) will be used for session authentication.
- OAuth login via Google and Facebook should be supported (future enhancement).
- Users must be able to login, logout, and update their profile.

### API Endpoints
| Method | Endpoint           | Description                  |
|--------|--------------------|------------------------------|
| POST   | `/api/register`    | Register a new user          |
| POST   | `/api/login`       | Authenticate and return JWT  |
| POST   | `/api/logout`      | Invalidate session token     |
| GET    | `/api/profile`     | Retrieve user profile        |
| PUT    | `/api/profile`     | Update user profile info     |

### Input/Output Specifications
- `POST /api/register`  
  **Input**: `{ "email": "user@example.com", "password": "pass1234" }`  
  **Output**: `{ "message": "User registered", "token": "<jwt_token>" }`

- `POST /api/login`  
  **Input**: `{ "email": "user@example.com", "password": "pass1234" }`  
  **Output**: `{ "token": "<jwt_token>", "user": { ... } }`

### Validation Rules
- Email must be unique and follow valid format.
- Passwords must be at least 8 characters, with a mix of upper, lower, and digits.
- Passwords must be hashed using bcrypt.

### Performance Criteria
- Authentication responses must complete in under 300ms under normal load.
- JWT token expiration should be configurable and default to 1 hour.

## 2. Property Management

### Functional Requirements
- Hosts can create, read, update, and delete property listings.
- Listings must include title, description, price, location, availability dates, and amenities.
- Images are uploaded and stored using file storage (e.g., Cloudinary or local).

### API Endpoints
| Method | Endpoint             | Description                    |
|--------|----------------------|--------------------------------|
| POST   | `/api/properties`    | Add new property               |
| GET    | `/api/properties`    | Get all properties             |
| GET    | `/api/properties/:id`| Get single property            |
| PUT    | `/api/properties/:id`| Update property                |
| DELETE | `/api/properties/:id`| Delete property                |

### Input/Output Specifications
- `POST /api/properties`  
  **Input**: `{ "title": "Cozy Room", "price": 35, "location": "Addis Ababa", ... }`  
  **Output**: `{ "message": "Property created", "id": 1 }`

- `GET /api/properties?location=Addis`  
  **Output**: `[{ "id": 1, "title": "Cozy Room", ... }]`

### Validation Rules
- Price must be a positive number.
- Title must be at least 10 characters.
- Only hosts can create or edit properties.
- Maximum of 10 images per property.

### Performance Criteria
- Paginate listing results with 10 properties per page.
- Indexed search on `location`, `price`, and `amenities` for fast filtering.


## 3. Booking System

### Functional Requirements
- Guests can book properties for available dates.
- Prevent double-bookings.
- Bookings can be confirmed, cancelled, or completed.
- Users can view their bookings.

### API Endpoints
| Method | Endpoint                  | Description                 |
|--------|---------------------------|-----------------------------|
| POST   | `/api/bookings`           | Create a new booking        |
| GET    | `/api/bookings`           | Get all user bookings       |
| GET    | `/api/bookings/:id`       | Get booking by ID           |
| DELETE | `/api/bookings/:id`       | Cancel booking              |

### Input/Output Specifications
- `POST /api/bookings`  
  **Input**: `{ "property_id": 2, "start_date": "2025-06-01", "end_date": "2025-06-05" }`  
  **Output**: `{ "message": "Booking successful", "booking_id": 5 }`

### Validation Rules
- Date ranges must not overlap with existing bookings.
- Start date must be before end date.
- Booking must be at least 1 night long.
- Only guests can create bookings.

### Performance Criteria
- Booking creation must check for availability in under 200ms.
- Index on `(property_id, start_date, end_date)` for fast conflict checking.

