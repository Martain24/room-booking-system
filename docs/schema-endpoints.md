# Endpoints

The API for the room booking system exposes the following RESTful endpoints.

## Users

### POST /api/auth/login — Authenticate User

Authenticates a user with a valid username and password. If the credentials are correct, returns a signed JWT token that must be used in subsequent authenticated requests.

* **Request Body:**

    ```json
    {
      "username": "jhon",
      "password": "pass"
    }
    ```

* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Login successful",
      "data": {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "expires_in": 3600
      }
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if the username or password is incorrect.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid username or password",
      "data": null
    }
    ```

### POST /api/users — Create User

Creates a new user in the system. The user is registered with the default role USER. Passwords are securely stored using hashing. The username must be unique.

* **Request Body:**

    ```json
    {
      "username": "jhon",
      "password": "pass"
    }
    ```

* **Successful Response — 201 Created:**
    Returned when the user is successfully created.

    ```json
    {
      "flag": true,
      "status": 201,
      "message": "User created successfully",
      "data": {
        "id": 1,
        "username": "jhon",
        "role": "USER",
        "created_at": "2025-05-24T16:03:20"
      }
    }
    ```

* **Error Response — 409 Conflict:**
    Returned when the provided username already exists in the system.

    ```json
    {
      "flag": false,
      "status": 409,
      "message": "Username already exists",
      "data": null
    }
    ```

* **Error Response — 400 Bad Request:**
    Returned when the input validation fails, e.g. password does not meet security requirements.

    ```json
    {
      "flag": false,
      "status": 400,
      "message": "Password is not safe enough",
      "data": {
        "validation_errors": [
          "Password must be at least 9 characters long",
          "Password must contain at least one number"
        ]
      }
    }
    ```

### PUT /api/users — Update Authenticated User

Updates the authenticated user's username and password. The user is identified through the JWT token provided in the Authorization header. Both fields are required. The password is hashed before being stored, and the new username must be unique.

* **Authorization:** The request must include a valid JWT token in the header: `Authorization: Bearer <token>`
* **Request Body:**

    ```json
    {
      "username": "new_username",
      "password": "new_password"
    }
    ```

* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "User updated successfully",
      "data": {
        "id": 1,
        "username": "new_username",
        "role": "USER",
        "updated_at": "2025-05-24T18:35:10"
      }
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if the JWT token is missing, expired, or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 409 Conflict:**
    Returned if the new username is already taken.

    ```json
    {
      "flag": false,
      "status": 409,
      "message": "Username already exists",
      "data": null
    }
    ```

* **Error Response — 400 Bad Request:**
    Returned when input validation fails (e.g., weak password, missing fields).

    ```json
    {
      "flag": false,
      "status": 400,
      "message": "Invalid input data",
      "data": {
        "validation_errors": [
          "Username is required",
          "Password must be at least 9 characters long",
          "Password must contain at least one number"
        ]
      }
    }
    ```

### DELETE /api/users — Delete Current User

Deletes the account of the currently authenticated user. Requires a valid JWT token in the Authorization header. Once deleted, the user cannot log in again and all related data will be permanently removed (según la política del sistema).

* **Headers:** `Authorization: Bearer <JWT token>`
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "User account deleted successfully",
      "data": null
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if no valid token is provided or the token is invalid/expired.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

### DELETE /api/users/{id} — Delete User by ID

Deletes a user by their unique ID. Only users with the ADMIN role can perform this operation. Requires a valid JWT token with admin privileges in the Authorization header.

* **Path Parameter:** `id` (Integer) - ID of the user to delete
* **Headers:** `Authorization: Bearer <JWT token>` (JWT token of an authenticated admin user)
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "User deleted successfully",
      "data": null
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user does not have ADMIN role.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Admin role required",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the user with the given ID does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "User not found",
      "data": null
    }
    ```

### GET /api/users — Get All Users

Returns a list of all users in the system. Only accessible by users with the ADMIN role. Requires a valid JWT token with admin privileges.

* **Headers:** `Authorization: Bearer <JWT token>` (JWT token of an authenticated admin user)
* **Query Parameters (Optional):**
    * `page` (Integer, default: 1): The page number to retrieve.
    * `limit` (Integer, default: 10): The number of users per page.

    **Example URL with Query Parameters:**
    `GET /api/users?page=2&limit=5` (This would retrieve the second page with 5 users per page).
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Users retrieved successfully",
      "data": [
        {
          "id": 1,
          "username": "jhon",
          "role": "USER",
          "created_at": "2025-05-24T16:03:20"
        },
        {
          "id": 2,
          "username": "adminuser",
          "role": "ADMIN",
          "created_at": "2025-05-20T09:15:00"
        }
      ]
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user does not have the ADMIN role.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Admin role required",
      "data": null
    }
    ```

### PUT /api/users/{id}/make-admin — Make User ADMIN

Grants the ADMIN role to the user identified by the given ID. Only users with the ADMIN role can perform this action.

* **Authorization:** Requires a valid JWT token with ADMIN role.
* **Path Parameter:** `id` (Integer) - ID of the user to promote to ADMIN
* **Request Body:** *No request body required.*
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "User role updated to ADMIN successfully",
      "data": {
        "id": 3,
        "username": "alice",
        "role": "ADMIN",
        "created_at": "2025-05-24T15:00:00"
      }
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the requester does not have the ADMIN role.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Access denied: Admin role required",
      "data": null
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the user with the given ID does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "User not found",
      "data": null
    }
    ```

---

## Meeting Rooms

### POST /api/meeting-rooms — Create Meeting Room

Creates a new meeting room. Only accessible by users with the ADMIN role. Requires a valid JWT token with admin privileges.

* **Headers:** `Authorization: Bearer <JWT token>` (JWT token of an authenticated admin user)
* **Request Body:**

    ```json
    {
      "name": "Meeting Room A",
      "description": "Room for up to 10 people",
      "location": "1st Floor",
      "max_people": 10,
      "has_projector": true,
      "has_whiteboard": true,
      "has_tv": false
    }
    ```

* **Successful Response — 201 Created:**

    ```json
    {
      "flag": true,
      "status": 201,
      "message": "Meeting room created successfully",
      "data": {
        "id": 1,
        "name": "Meeting Room A",
        "description": "Room for up to 10 people",
        "location": "1st Floor",
        "max_people": 10,
        "has_projector": true,
        "has_whiteboard": true,
        "has_tv": false,
        "created_at": "2025-05-24T19:00:00"
      }
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user does not have the ADMIN role.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Admin role required",
      "data": null
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 400 Bad Request:**
    Returned if the input validation fails.

    ```json
    {
      "flag": false,
      "status": 400,
      "message": "Invalid input data",
      "data": {
        "validation_errors": [
          "Name is required",
          "Max people must be a positive number"
        ]
      }
    }
    ```

### GET /api/meeting-rooms — Get All Meeting Rooms

Returns a list of all meeting rooms. Accessible by authenticated users. Requires a valid JWT token.

* **Headers:** `Authorization: Bearer <JWT token>`
* **Query Parameters (Optional):**
    * `page` (Integer, default: 1): The page number to retrieve.
    * `limit` (Integer, default: 10): The number of meeting rooms per page.

    **Example URL with Query Parameters:**
    `GET /api/meeting-rooms?page=1&limit=5` (This would retrieve the first page with 5 meeting rooms per page).
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Meeting rooms retrieved successfully",
      "data": [
        {
          "id": 1,
          "name": "Meeting Room A",
          "description": "Room for up to 10 people",
          "location": "1st Floor",
          "max_people": 10,
          "has_projector": true,
          "has_whiteboard": true,
          "has_tv": false
        },
        {
          "id": 2,
          "name": "Conference Room B",
          "description": "Large conference room with TV",
          "location": "2nd Floor",
          "max_people": 20,
          "has_projector": false,
          "has_whiteboard": true,
          "has_tv": true
        }
      ]
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

### GET /api/meeting-rooms/{id} — Get Meeting Room by ID

Returns a single meeting room by its unique ID. Accessible by authenticated users. Requires a valid JWT token.

* **Path Parameter:** `id` (Integer) - ID of the meeting room to retrieve
* **Headers:** `Authorization: Bearer <JWT token>`
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Meeting room retrieved successfully",
      "data": {
        "id": 1,
        "name": "Meeting Room A",
        "description": "Room for up to 10 people",
        "location": "1st Floor",
        "max_people": 10,
        "has_projector": true,
        "has_whiteboard": true,
        "has_tv": false
      }
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the meeting room with the given ID does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Meeting room not found",
      "data": null
    }
    ```

### PUT /api/meeting-rooms/{id} — Update Meeting Room

Updates an existing meeting room by its ID. Only accessible by users with the ADMIN role. Requires a valid JWT token with admin privileges.

* **Path Parameter:** `id` (Integer) - ID of the meeting room to update
* **Headers:** `Authorization: Bearer <JWT token>` (JWT token of an authenticated admin user)
* **Request Body:**

    ```json
    {
      "name": "Updated Meeting Room A",
      "description": "Larger room for 12 people",
      "location": "1st Floor, West Wing",
      "max_people": 12,
      "has_projector": true,
      "has_whiteboard": true,
      "has_tv": true
    }
    ```
    *Note: All fields are generally expected in a PUT request, even if only one is changing, to represent a full replacement of the resource.*
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Meeting room updated successfully",
      "data": {
        "id": 1,
        "name": "Updated Meeting Room A",
        "description": "Larger room for 12 people",
        "location": "1st Floor, West Wing",
        "max_people": 12,
        "has_projector": true,
        "has_whiteboard": true,
        "has_tv": true,
        "updated_at": "2025-05-24T19:30:00"
      }
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user does not have the ADMIN role.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Admin role required",
      "data": null
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the meeting room with the given ID does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Meeting room not found",
      "data": null
    }
    ```

* **Error Response — 400 Bad Request:**
    Returned if the input validation fails.

    ```json
    {
      "flag": false,
      "status": 400,
      "message": "Invalid input data",
      "data": {
        "validation_errors": [
          "Name cannot be empty",
          "Max people must be positive"
        ]
      }
    }
    ```

### DELETE /api/meeting-rooms/{id} — Delete Meeting Room

Deletes a meeting room by its ID. Only accessible by users with the ADMIN role. Requires a valid JWT token with admin privileges.

* **Path Parameter:** `id` (Integer) - ID of the meeting room to delete
* **Headers:** `Authorization: Bearer <JWT token>` (JWT token of an authenticated admin user)
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Meeting room deleted successfully",
      "data": null
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user does not have the ADMIN role.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Admin role required",
      "data": null
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the meeting room with the given ID does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Meeting room not found",
      "data": null
    }
    ```

---

## Meetings

### POST /api/meetings — Create Meeting

Creates a new meeting. Accessible by any authenticated user. The `created_by` field will be automatically set to the ID of the authenticated user.

* **Headers:** `Authorization: Bearer <JWT token>` (JWT token of an authenticated user)
* **Request Body:**

    ```json
    {
      "title": "Project Alpha Review",
      "description": "Discuss progress on Project Alpha",
      "start_time": "2025-06-01T10:00:00Z",
      "end_time": "2025-06-01T11:00:00Z",
      "is_online": false,
      "meeting_room_id": 1
    }
    ```
    *Note: If `is_online` is true, `meeting_room_id` should be null or omitted.*
* **Successful Response — 201 Created:**

    ```json
    {
      "flag": true,
      "status": 201,
      "message": "Meeting created successfully",
      "data": {
        "id": 1,
        "title": "Project Alpha Review",
        "description": "Discuss progress on Project Alpha",
        "start_time": "2025-06-01T10:00:00Z",
        "end_time": "2025-06-01T11:00:00Z",
        "is_online": false,
        "meeting_room_id": 1,
        "created_by": 5,
        "created_at": "2025-05-24T20:00:00Z"
      }
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 400 Bad Request:**
    Returned if input validation fails (e.g., missing fields, invalid dates, `end_time` before `start_time`).

    ```json
    {
      "flag": false,
      "status": 400,
      "message": "Invalid input data",
      "data": {
        "validation_errors": [
          "Title is required",
          "End time must be after start time"
        ]
      }
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if `is_online` is false but `meeting_room_id` does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Meeting room not found",
      "data": null
    }
    ```

* **Error Response — 409 Conflict:**
    Returned if the meeting room is already booked for the specified time slot.

    ```json
    {
      "flag": false,
      "status": 409,
      "message": "Meeting room is already booked for this time slot",
      "data": null
    }
    ```

### GET /api/meetings — Get All Meetings

Returns a list of all meetings. Accessible by any authenticated user. Users can typically only see meetings they are invited to or created, unless they are an ADMIN. This endpoint would list all meetings *visible* to the authenticated user.

* **Headers:** `Authorization: Bearer <JWT token>`
* **Query Parameters (Optional):**
    * `page` (Integer, default: 1): The page number to retrieve.
    * `limit` (Integer, default: 10): The number of meetings per page.
    * `my_meetings` (Boolean, default: false): If true, returns only meetings created by or participated in by the authenticated user.
    * `meeting_room_id` (Integer): Filters meetings by a specific meeting room.
    * `start_time_after` (Timestamp): Filters meetings starting after this time.
    * `end_time_before` (Timestamp): Filters meetings ending before this time.

    **Example URL with Query Parameters:**
    `GET /api/meetings?page=1&limit=10&my_meetings=true&start_time_after=2025-06-01T00:00:00Z`
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Meetings retrieved successfully",
      "data": [
        {
          "id": 1,
          "title": "Project Alpha Review",
          "description": "Discuss progress on Project Alpha",
          "start_time": "2025-06-01T10:00:00Z",
          "end_time": "2025-06-01T11:00:00Z",
          "is_online": false,
          "meeting_room_id": 1,
          "created_by": 5,
          "created_at": "2025-05-24T20:00:00Z"
        },
        {
          "id": 2,
          "title": "Team Sync",
          "description": null,
          "start_time": "2025-06-02T14:00:00Z",
          "end_time": "2025-06-02T14:30:00Z",
          "is_online": true,
          "meeting_room_id": null,
          "created_by": 2,
          "created_at": "2025-05-23T10:00:00Z"
        }
      ]
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

### GET /api/meetings/{id} — Get Meeting by ID

Returns a single meeting by its unique ID. Accessible by authenticated users who created the meeting, are participants, or have the ADMIN role.

* **Path Parameter:** `id` (Integer) - ID of the meeting to retrieve
* **Headers:** `Authorization: Bearer <JWT token>`
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Meeting retrieved successfully",
      "data": {
        "id": 1,
        "title": "Project Alpha Review",
        "description": "Discuss progress on Project Alpha",
        "start_time": "2025-06-01T10:00:00Z",
        "end_time": "2025-06-01T11:00:00Z",
        "is_online": false,
        "meeting_room_id": 1,
        "created_by": 5,
        "created_at": "2025-05-24T20:00:00Z"
      }
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user is not the creator, not a participant, and not an ADMIN.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Not authorized to view this meeting",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the meeting with the given ID does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Meeting not found",
      "data": null
    }
    ```

### PUT /api/meetings/{id} — Update Meeting

Updates an existing meeting by its ID. Only accessible by the user who created the meeting or by an ADMIN.

* **Path Parameter:** `id` (Integer) - ID of the meeting to update
* **Headers:** `Authorization: Bearer <JWT token>`
* **Request Body:**

    ```json
    {
      "title": "Project Alpha Final Review",
      "description": "Final discussion and wrap-up",
      "start_time": "2025-06-01T10:00:00Z",
      "end_time": "2025-06-01T12:00:00Z",
      "is_online": false,
      "meeting_room_id": 1
    }
    ```
    *Note: All fields are generally expected in a PUT request, even if only one is changing, to represent a full replacement of the resource. `created_by` and `created_at` are read-only.*
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Meeting updated successfully",
      "data": {
        "id": 1,
        "title": "Project Alpha Final Review",
        "description": "Final discussion and wrap-up",
        "start_time": "2025-06-01T10:00:00Z",
        "end_time": "2025-06-01T12:00:00Z",
        "is_online": false,
        "meeting_room_id": 1,
        "created_by": 5,
        "created_at": "2025-05-24T20:00:00Z"
      }
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user is not the creator of the meeting and not an ADMIN.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Not authorized to update this meeting",
      "data": null
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the meeting with the given ID does not exist, or if `meeting_room_id` is provided but does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Meeting not found",
      "data": null
    }
    ```

* **Error Response — 400 Bad Request:**
    Returned if input validation fails.

    ```json
    {
      "flag": false,
      "status": 400,
      "message": "Invalid input data",
      "data": {
        "validation_errors": [
          "Title cannot be empty"
        ]
      }
    }
    ```

* **Error Response — 409 Conflict:**
    Returned if the updated meeting time conflicts with another booking in the same room.

    ```json
    {
      "flag": false,
      "status": 409,
      "message": "Meeting room is already booked for this time slot",
      "data": null
    }
    ```

### DELETE /api/meetings/{id} — Delete Meeting

Deletes a meeting by its ID. Only accessible by the user who created the meeting or by an ADMIN.

* **Path Parameter:** `id` (Integer) - ID of the meeting to delete
* **Headers:** `Authorization: Bearer <JWT token>`
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Meeting deleted successfully",
      "data": null
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user is not the creator of the meeting and not an ADMIN.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Not authorized to delete this meeting",
      "data": null
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the meeting with the given ID does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Meeting not found",
      "data": null
    }
    ```

---

## Meeting Participants

### POST /api/meetings/{meeting_id}/participants — Add Participant to Meeting

Adds a user as a participant to a specific meeting. Only the meeting creator or an ADMIN can add participants.

* **Path Parameter:** `meeting_id` (Integer) - ID of the meeting
* **Headers:** `Authorization: Bearer <JWT token>`
* **Request Body:**

    ```json
    {
      "user_id": 2,
      "status": "pending"
    }
    ```
    *Note: `status` can be "confirmed", "pending", "cancelled". If omitted, "pending" is a good default.*
* **Successful Response — 201 Created:**

    ```json
    {
      "flag": true,
      "status": 201,
      "message": "Participant added successfully",
      "data": {
        "meeting_id": 1,
        "user_id": 2,
        "status": "pending",
        "joined_at": "2025-05-24T20:15:00Z"
      }
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user is not the meeting creator and not an ADMIN.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Not authorized to add participants to this meeting",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the `meeting_id` or `user_id` does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Meeting or User not found",
      "data": null
    }
    ```

* **Error Response — 400 Bad Request:**
    Returned if input validation fails (e.g., invalid status, user already a participant).

    ```json
    {
      "flag": false,
      "status": 400,
      "message": "Invalid input data",
      "data": {
        "validation_errors": [
          "User is already a participant in this meeting"
        ]
      }
    }
    ```

### GET /api/meetings/{meeting_id}/participants — Get Meeting Participants

Returns a list of all participants for a specific meeting. Accessible by authenticated users who created the meeting, are participants, or have the ADMIN role.

* **Path Parameter:** `meeting_id` (Integer) - ID of the meeting
* **Headers:** `Authorization: Bearer <JWT token>`
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Meeting participants retrieved successfully",
      "data": [
        {
          "user_id": 5,
          "username": "creator_user",
          "status": "confirmed",
          "joined_at": "2025-05-24T20:00:00Z"
        },
        {
          "user_id": 2,
          "username": "invited_user",
          "status": "pending",
          "joined_at": "2025-05-24T20:15:00Z"
        }
      ]
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user is not the meeting creator, not a participant, and not an ADMIN.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Not authorized to view participants of this meeting",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the `meeting_id` does not exist.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Meeting not found",
      "data": null
    }
    ```

### PUT /api/meetings/{meeting_id}/participants/{user_id} — Update Participant Status

Updates the status of a participant in a meeting. This can be done by the participant themselves (to confirm/cancel their attendance), the meeting creator, or an ADMIN.

* **Path Parameters:**
    * `meeting_id` (Integer) - ID of the meeting
    * `user_id` (Integer) - ID of the participant to update
* **Headers:** `Authorization: Bearer <JWT token>`
* **Request Body:**

    ```json
    {
      "status": "confirmed"
    }
    ```
    *Note: `status` can be "confirmed", "pending", "cancelled".*
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Participant status updated successfully",
      "data": {
        "meeting_id": 1,
        "user_id": 2,
        "status": "confirmed",
        "joined_at": "2025-05-24T20:15:00Z"
      }
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user is not the participant being updated, not the meeting creator, and not an ADMIN.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Not authorized to update this participant's status",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the `meeting_id` or `user_id` does not exist in the participant list for that meeting.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Participant not found in this meeting",
      "data": null
    }
    ```

* **Error Response — 400 Bad Request:**
    Returned if the provided `status` value is invalid.

    ```json
    {
      "flag": false,
      "status": 400,
      "message": "Invalid status value",
      "data": null
    }
    ```

### DELETE /api/meetings/{meeting_id}/participants/{user_id} — Remove Participant from Meeting

Removes a user as a participant from a specific meeting. This can be done by the participant themselves (leaving the meeting), the meeting creator, or an ADMIN.

* **Path Parameters:**
    * `meeting_id` (Integer) - ID of the meeting
    * `user_id` (Integer) - ID of the participant to remove
* **Headers:** `Authorization: Bearer <JWT token>`
* **Successful Response — 200 OK:**

    ```json
    {
      "flag": true,
      "status": 200,
      "message": "Participant removed successfully",
      "data": null
    }
    ```

* **Error Response — 401 Unauthorized:**
    Returned if authentication token is missing or invalid.

    ```json
    {
      "flag": false,
      "status": 401,
      "message": "Unauthorized: Invalid or missing token",
      "data": null
    }
    ```

* **Error Response — 403 Forbidden:**
    Returned if the authenticated user is not the participant being removed, not the meeting creator, and not an ADMIN.

    ```json
    {
      "flag": false,
      "status": 403,
      "message": "Forbidden: Not authorized to remove this participant",
      "data": null
    }
    ```

* **Error Response — 404 Not Found:**
    Returned if the `meeting_id` or `user_id` does not exist in the participant list for that meeting.

    ```json
    {
      "flag": false,
      "status": 404,
      "message": "Participant not found in this meeting",
      "data": null
    }
    ```