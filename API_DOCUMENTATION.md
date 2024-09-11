# API Documentation

This document provides an overview of the API endpoints and their usage.

> **Note**: The authentication and session management mechanisms rely on cookies for maintaining user sessions. Therefore, clients interacting with these endpoints should ensure they properly handle cookies (e.g., storing and sending them with each request) to maintain authenticated states throughout the session.

## Table of Contents

- [Authentication & Session Management](#authentication--session-management)
  - [User Authentication](#user-authentication)
    - [Get Email Verification Code](#get-email-verification-code)
    - [Register User](#register-user)
    - [Login User](#login-user)
    - [Logout User](#logout-user)
    - [Two-Factor Authentication](#two-factor-authentication)
    - [Reset Password By Email](#reset-password-by-email)
  - [Session Management](#session-management)
    - [Get Current Session](#get-current-session)
    - [Get All User Sessions](#get-all-user-sessions)
    - [Delete Session](#delete-session)
    - [Delete All Sessions (Except Current)](#delete-all-sessions-except-current)
    - [Delete All Sessions Except One](#delete-all-sessions-except-one)
    - [Delete All Sessions](#delete-all-sessions)

## Authentication & Session Management

### User Authentication

#### Get Email Verification Code

- **Description**: Request a verification code to be sent to the user's email address.

- **Endpoint**: `POST /users/verification-code`

- **Request Body**:

  ```json
  {
    "email": "string"
  }
  ```

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "message": "Verification code sent successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Invalid email address",
      "error": {
        "code": "INVALID_EMAIL"
      }
    }
    ```

  - **Error**: `429 Too Many Requests`

    ```json
    {
      "status": "error",
      "message": "Too many requests. Please try again later.",
      "error": {
        "code": "TOO_MANY_REQUESTS",
        "details": {
          "nextRequestTime": 1620000000000
        }
      }
    }
    ```

  - **Error**: `500 Internal Server Error`

    ```json
    {
      "status": "error",
      "message": "Error sending verification code.",
      "error": {
        "code": "SEND_ERROR"
      }
    }
    ```

#### Register User

- **Description**: Register a new user account.

- **Endpoint**: `POST /users/register`

- **Request Body**:

  ```json
  {
    "email": "string",
    "password": "string",
    "username": "string",
    "code": 123456
  }
  ```

- **Response**:

  - **Success**: `200 OK`

  ```json
  {
    "status": "success",
    "data": {
      "username": "string",
      "email": "string",
      "profile": {
        "firstName": "string",
        "lastName": "string",
        "gender": null,
        "location": "string",
        "birthday": null,
        "bio": "string",
        "avatar": "string",
        "customizeUrls": []
      },
      "experience": 0,
      "identity": [],
      "suspended": false,
      "_id": "string",
      "safetyRecords": [],
      "createdAt": "string",
      "updatedAt": "string",
      "__v": 0
    },
    "message": "User registered successfully."
  }
  ```

  > The `gender` field is `null` if not specified, `true` is male, and `false` is female.</br>
  > User will be logged in automatically after registration.

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "All fields are required",
      "error": {
        "code": "MISSING_FIELDS",
        "details": {
          "email": "string",
          "password": "string",
          "username": "string",
          "code": 123456
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Invalid verification code, please get a new one",
      "error": {
        "code": "INVALID_CODE"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Verification code expired",
      "error": {
        "code": "CODE_EXPIRED"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Email does not match",
      "error": {
        "code": "EMAIL_MISMATCH"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Password must be at least 6 characters long",
      "error": {
        "code": "PASSWORD_TOO_SHORT"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username must be at least 3 characters long",
      "error": {
        "code": "USERNAME_TOO_SHORT"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username must be at most 20 characters long",
      "error": {
        "code": "USERNAME_TOO_LONG"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username must contain only letters, numbers, underscores, and hyphens",
      "error": {
        "code": "INVALID_USERNAME"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Invalid email address",
      "error": {
        "code": "INVALID_EMAIL"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Email already in use",
      "error": {
        "code": "EMAIL_IN_USE"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username already in use",
      "error": {
        "code": "USERNAME_IN_USE"
      }
    }
    ```

  - **Error**: `500 Internal Server Error`

    ```json
    {
      "status": "error",
      "message": "Error registering user.",
      "error": {
        "code": "REGISTER_ERROR"
      }
    }
    ```

#### Login User

- **Description**: Log in an existing user account.

- **Endpoint**: `POST /users/login`

- **Request Body**:

  ```json
  {
    "identifier": "string",
    "password": "string"
  }
  ```

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "username": "string",
        "email": "string",
        "profile": {
          "firstName": "string",
          "lastName": "string",
          "gender": null,
          "location": "string",
          "birthday": null,
          "bio": "string",
          "avatar": "string",
          "customizeUrls": []
        },
        "experience": 0,
        "identity": [],
        "suspended": false,
        "_id": "string",
        "createdAt": "string",
        "updatedAt": "string",
        "__v": 0
      },
      "message": "User logged in successfully."
    }
    ```

  > The `message` may vary if 2FA is required. (e.g., "User logged in successfully. 2FA required.")</br>
  > The `gender` field is `null` if not specified, `true` is male, and `false` is female.

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "All fields are required",
      "error": {
        "code": "MISSING_FIELDS",
        "details": {
          "identifier": "string",
          "password": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "User already logged in",
      "error": {
        "code": "ALREADY_LOGGED_IN"
      }
    }
    ```

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND"
      }
    }
    ```

  - **Error**: `401 Unauthorized`

    ```json
    {
      "status": "error",
      "message": "Invalid password",
      "error": {
        "code": "INVALID_PASSWORD"
      }
    }
    ```

  - **Error**: `500 Internal Server Error`

    ```json
    {
      "status": "error",
      "message": "Error logging in user",
      "error": {
        "code": "LOGIN_ERROR"
      }
    }
    ```

#### Logout User

- **Description**: Log out the current user. (Clears the session)

- **Endpoint**: `POST /users/logout`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "message": "User logged out successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "User not logged in",
      "error": {
        "code": "NOT_LOGGED_IN"
      }
    }
    ```

#### Two-Factor Authentication

- **Description**: Verify the two-factor authentication code.

- **Endpoint**: `POST /users/2fa`

- **Request Body**:

  ```json
  {
    "code": 123456
  }
  ```

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "message": "User logged in successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "2FA not required",
      "error": {
        "code": "2FA_NOT_REQUIRED"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Verification code is required",
      "error": {
        "code": "NO_VERIFICATION_CODE"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "All fields are required",
      "error": {
        "code": "MISSING_FIELDS"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Verification code expired",
      "error": {
        "code": "CODE_EXPIRED"
      }
    }
    ```

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Email does not match",
      "error": {
        "code": "EMAIL_MISMATCH"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Invalid verification code, please get a new one",
      "error": {
        "code": "INVALID_CODE"
      }
    }
    ```

  - **Error**: `401 Unauthorized`

    ```json
    {
      "status": "error",
      "message": "User not logged in",
      "error": {
        "code": "NOT_LOGGED_IN"
      }
    }
    ```

  - **Error**: `500 Internal Server Error`

    ```json
    {
      "status": "error",
      "message": "Error logging in user",
      "error": {
        "code": "LOGIN_ERROR"
      }
    }
    ```

> Please get a verification code before attempting to log in (Two-Factor Authentication). Use the [`Get Email Verification Code`](#get-email-verification-code) endpoint.

#### Reset Password By Email

- **Description**: Reset the user's password using their email address.

- **Endpoint**: `POST /users/reset-password-by-email`

- **Request Body**:

  ```json
  {
    "email": "string",
    "password": "string",
    "code": 123456
  }
  ```

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "message": "Password reset successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Email verification code is required",
      "error": {
        "code": "NO_VERIFICATION_CODE"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Email is required",
      "error": {
        "code": "MISSING_EMAIL",
        "details": {
          "email": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Verification code expired",
      "error": {
        "code": "CODE_EXPIRED"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Email does not match",
      "error": {
        "code": "EMAIL_MISMATCH"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Invalid verification code, please get a new one",
      "error": {
        "code": "INVALID_CODE"
      }
    }
    ```

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Password is required",
      "error": {
        "code": "MISSING_PASSWORD",
        "details": {
          "password": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Password must be at least 6 characters long",
      "error": {
        "code": "PASSWORD_TOO_SHORT"
      }
    }
    ```

  - **Error**: `500 Internal Server Error`

    ```json
    {
      "status": "error",
      "message": "Error updating user",
      "error": {
        "code": "UPDATE_ERROR"
      }
    }
    ```

> Please get a verification code before attempting to reset the password. Use the [`Get Email Verification Code`](#get-email-verification-code) endpoint.

### Session Management

#### Get Current Session

- **Description**: Get the current session status.

- **Endpoint**: `GET /sessions/current`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "authenticated": true,
        "twoFactorAuthenticated": true,
        "userId": "string",
        "sessionId": "string"
      },
      "message": "Current session status retrieved successfully."
    }
    ```

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "authenticated": true,
        "twoFactorAuthenticated": false,
        "userId": "string"
      },
      "message": "Current session status retrieved successfully."
    }
    ```

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "authenticated": false,
        "twoFactorAuthenticated": false,
        "userId": null
      },
      "message": "Current session status retrieved successfully."
    }
    ```

#### Get All User Sessions

- **Description**: Get all sessions for a user.

- **Endpoint**: `GET /sessions/user/:userId`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": [
        {
          "sessionId": "string",
          "authenticated": true,
          "twoFactorAuthenticated": true,
          "userId": "string"
        }
      ],
      "message": "User sessions listed successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "User ID is required",
      "error": {
        "code": "MISSING_USER_ID",
        "details": {
          "userId": "string"
        }
      }
    }
    ```

  - **Error**: `403 Forbidden`

    ```json
    {
      "status": "error",
      "message": "Access denied",
      "error": {
        "code": "ACCESS_DENIED"
      }
    }
    ```

#### Delete Session

- **Description**: Delete a specific session.

- **Endpoint**: `DELETE /sessions/:sessionId`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "message": "Session deleted successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Session ID is required",
      "error": {
        "code": "MISSING_SESSION_ID",
        "details": {
          "sessionId": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Cannot delete the current session",
      "error": {
        "code": "CANNOT_DELETE_CURRENT_SESSION",
        "details": {
          "sessionId": "string"
        }
      }
    }
    ```

  - **Error**: `403 Forbidden`

    ```json
    {
      "status": "error",
      "message": "Access denied",
      "error": {
        "code": "ACCESS_DENIED"
      }
    }
    ```

#### Delete All Sessions (Except Current)

- **Description**: Delete all sessions for the user except the current session.

- **Endpoint**: `DELETE /sessions/user/:userId/exclude-current`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "message": "Sessions deleted successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "User ID is required",
      "error": {
        "code": "MISSING_USER_ID",
        "details": {
          "userId": "string"
        }
      }
    }
    ```

  - **Error**: `403 Forbidden`

    ```json
    {
      "status": "error",
      "message": "Access denied",
      "error": {
        "code": "ACCESS_DENIED"
      }
    }
    ```

#### Delete All Sessions Except One

- **Description**: Delete all sessions for the user except one specified session.

- **Endpoint**: `DELETE /sessions/user/:userId/exclude/:excludeSessionId`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "message": "Sessions deleted successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "User ID is required",
      "error": {
        "code": "MISSING_USER_ID",
        "details": {
          "userId": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Session ID is required",
      "error": {
        "code": "MISSING_SESSION_ID",
        "details": {
          "sessionId": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Cannot delete the current session",
      "error": {
        "code": "CANNOT_DELETE_CURRENT_SESSION",
        "details": {
          "sessionId": "string"
        }
      }
    }
    ```

  - **Error**: `403 Forbidden`

    ```json
    {
      "status": "error",
      "message": "Access denied",
      "error": {
        "code": "ACCESS_DENIED"
      }
    }
    ```

#### Delete All Sessions

- **Description**: Delete all sessions for the user.

- **Endpoint**: `DELETE /sessions/user/:userId`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "message": "Sessions deleted successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "User ID is required",
      "error": {
        "code": "MISSING_USER_ID",
        "details": {
          "userId": "string"
        }
      }
    }
    ```

  - **Error**: `403 Forbidden`

    ```json
    {
      "status": "error",
      "message": "Access denied",
      "error": {
        "code": "ACCESS_DENIED"
      }
    }
    ```
