# Stolen Car Registry

## Overview
The **Stolen Car Registry API** is a centralized platform designed to register, track, 
and report stolen vehicles. This API allows users to input and retrieve essential details 
related to vehicle theft, such as car make, model, color, license plate number, VIN, year, 
date of theft, theft location, reporting officer, and status. It aims to assist law enforcement, 
insurance companies, and car owners in managing theft records efficiently and improving 
the chances of vehicle recovery.


### Requirements

1. **General Features**:
   - Register stolen vehicles with comprehensive information.
   - Retrieve and update the status of reported stolen vehicles.
   - Search for vehicles by license plate, VIN, or other identifying information.
   - Track a vehicle's recovery status in real time.  


2. **User Features**:
   - Guest users will be able to search for vehicles by license plate and/or VIN
   - Registered users will be able to register, update or delete registered vehicles


3. **Car Information Collection**:
   - Data fields: car model, car make, color, license plate number, VIN number, year, date and time stolen, theft location, etc.
   - Police report details: reported stolen date, police officer who made the report.
   - Status tracking: current status (found or missing).
   - Images of the stolen vehicle.


4. **CRUD Operations**:
   - Register a stolen car (create report).
   - Retrieve details of a specific stolen car (by VIN, license plate, or report ID).
   - Update the status of a car (mark it as found).
   - List all reported stolen cars with optional filters.


5. **Data Validation and Security**:
   - Validate inputs such as VIN, license plate format, and required fields.
   - Secure sensitive information (e.g., data on police officers).


6. **Images Handling**:
   - Store and retrieve images for each report.
   - Restrict file size and type for images.

### API Endpoints

Here's my proposed set of endpoints with details on their methods, required parameters, 
and expected responses.

### Authentication
For security, all endpoints (except `/auth/login`) require an authentication token. Register and log in to retrieve an access token.

#### POST /auth/login
- **Description**: Authenticate and receive a token.
- **Request Body**:
  ```json
  {
    "username": "user123",
    "password": "securepassword"
  }
  ```
- **Response**:
  ```json
  {
    "token": "access_token"
  }
  ```

---

### Vehicle Endpoints

#### 1. **Register a Stolen Car**
   - **Endpoint**: `POST /api/stolen-cars`
   - **Description**: Registers a new report of a stolen car.
   - **Request Body (JSON)**:
  ```json
  {
    "car_model": "string",
    "car_make": "string",
    "color": "string",
    "license_plate": "string",
    "vin_number": "string",
    "year": "integer",
    "date_time_stolen": "ISO 8601 datetime string",
    "theft_location": "string",
    "reported_stolen": true,
    "reported_stolen_date": "ISO 8601 datetime string",
    "police_officer": "string",
    "status": "missing",
    "images": ["base64 encoded image strings"]
  }
  ```
- **Response**:
  ```json
  {
    "success": true,
    "message": "Stolen car report created successfully",
    "report_id": "uuid"
  }
  ```

#### 2. **Retrieve Stolen Car Details**
   - **Endpoint**: `GET /api/stolen-cars/{report_id}`
   - **Description**: Fetches details of a stolen car report by its unique report ID.
   - **Path Parameters**:
     - `report_id` (string): Unique identifier for the report.
   - **Response (JSON)**:
     ```json
     {
       "report_id": "uuid",
       "car_model": "string",
       "car_make": "string",
       "color": "string",
       "license_plate": "string",
       "vin_number": "string",
       "year": "integer",
       "date_time_stolen": "ISO 8601 datetime string",
       "theft_location": "string",
       "reported_stolen": true,
       "reported_stolen_date": "ISO 8601 datetime string",
       "police_officer": "string",
       "status": "missing",
       "images": ["base64 encoded image strings"]
     }
     ```
   - **Errors**: Report not found, invalid `report_id`.

#### 3. **Update Stolen Car Status**
   - **Endpoint**: `PATCH /api/stolen-cars/{report_id}/status`
   - **Description**: Updates the status of a stolen car report.
   - **Path Parameters**:
     - `report_id` (string): Unique identifier for the report.
   - **Request Body (JSON)**:
     ```json
     {
       "status": "found"  // or "missing"
     }
     ```
   - **Response (JSON)**:
     ```json
     {
       "success": true,
       "message": "Car status updated successfully"
     }
     ```
   - **Errors**: Report not found, invalid `report_id`, invalid status value.

#### 4. **List All Stolen Cars**
   - **Endpoint**: `GET /api/stolen-cars`
   - **Description**: Retrieves a list of all stolen car reports with optional filters.
   - **Query Parameters** (optional):
     - `status` (string): Filter by status (`found` or `missing`).
     - `car_make` (string): Filter by car make.
     - `year` (integer): Filter by car year.
   - **Response (JSON)**:
     ```json
     {
       "reports": [
         {
           "report_id": "uuid",
           "car_model": "string",
           "car_make": "string",
           "license_plate": "string",
           "vin_number": "string",
           "status": "missing"
         },
         ...
       ]
     }
     ```
   - **Errors**: Invalid query parameters, no reports found (empty list).


#### 5. **Upload Images for Stolen Car**
   - **Endpoint**: `POST /api/stolen-cars/{report_id}/images`
   - **Description**: Uploads additional images to an existing stolen car report.
   - **Path Parameters**:
     - `report_id` (string): Unique identifier for the report.
   - **Request Body**:
     ```json
     {
       "images": ["base64 encoded image strings"]
     }
     ```
   - **Response (JSON)**:
     ```json
     {
       "success": true,
       "message": "Images uploaded successfully"
     }
     ```
   - **Errors**: Report not found, invalid image format, size limit exceeded.


## Data Model
- **car_model**: String, required
- **car_make**: String, required
- **color**: String, required
- **license_plate**: String, required, unique
- **vin_number**: String, required, unique
- **year**: Number, required
- **date_time_stolen**: ISO Date, required
- **theft_location**: String, required
- **reportingOfficer**: String, optional
- **reported_stolen**: Boolean
- **reported_stolen_date**: ISO Date, required
- **status**: String, required (e.g., "Missing", "Recovered")
- **images**: Array of image URLs, optional

## Error Handling
The API will return the following error codes for common issues:
- **400 Bad Request**: Invalid input data.
- **401 Unauthorized**: Invalid or missing authentication token.
- **404 Not Found**: Vehicle not found in the registry.
- **409 Duplicate**: Duplicate Vehicle found in the registry.
- **500 Internal Server Error**: Server encountered an issue.

## License
This project is licensed under the MIT License.

