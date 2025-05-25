# Database Schema

The SQL database for the room booking system will contain the following main tables:

## Table: Users

| Field        | Type            | Description                           |
| ------------ | --------------- | ------------------------------------- |
| `id`         | Integer (PK)    | Unique user identifier                |
| `username`   | String (unique) | Unique username                       |
| `password`   | String          | Hashed password                       |
| `role`       | String          | User role (e.g., MASTER, ADMIN, USER) |
| `created_at` | Timestamp       | Timestamp of record creation          |



## Table: Meeting Rooms

| Field            | Type            | Description                                      |
| ---------------- | --------------- | ------------------------------------------------ |
| `id`             | Integer (PK)    | Unique meeting room identifier                   |
| `name`           | String (unique) | Unique name of the meeting room                  |
| `description`    | String          | Textual description of the room                  |
| `location`       | String          | Address or location of the room                  |
| `max_people`     | Integer         | Maximum allowed capacity of the room             |
| `has_projector`  | Boolean         | Indicates if the room has a projector            |
| `has_whiteboard` | Boolean         | Indicates if the room has a whiteboard           |
| `has_tv`         | Boolean         | Indicates if the room has a TV for presentations |


## Table: Meetings

| Field             | Type                   | Description                                                  |
| ----------------- | ---------------------- | ------------------------------------------------------------ |
| `id`              | Integer (PK)           | Unique meeting identifier                                    |
| `title`           | String                 | Title or subject of the meeting                              |
| `description`     | String                 | Optional notes or description                                |
| `start_time`      | Timestamp              | Meeting start date and time                                  |
| `end_time`        | Timestamp              | Meeting end date and time                                    |
| `is_online`       | Boolean                | True if the meeting is online; false if in a meeting room    |
| `meeting_room_id` | Integer (FK, nullable) | Foreign key to `Meeting Rooms`; nullable for online meetings |
| `created_by`      | Integer (FK)           | Foreign key to `Users` who created the meeting               |
| `created_at`      | Timestamp              | Timestamp when the meeting was created                       |

*Note:* If `is_online` is true, `meeting_room_id` should be null; otherwise it must reference a valid meeting room.


## Table: Meeting Participants

| Field        | Type         | Description                                                      |
| ------------ | ------------ | ---------------------------------------------------------------- |
| `meeting_id` | Integer (FK) | Foreign key to the `Meetings` table                              |
| `user_id`    | Integer (FK) | Foreign key to the `Users` table                                 |
| `status`     | String       | Participation status (e.g., "confirmed", "pending", "cancelled") |
| `joined_at`  | Timestamp    | Date and time when the user joined the meeting                   |

* Composite primary key: `(meeting_id, user_id)`
* This table represents the many-to-many relationship between users and meetings, allowing to track attendance and participation status.