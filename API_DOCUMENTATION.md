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
- [User Management](#user-management)
  - [User Information & Profile](#user-information--profile)
    - [Get User By ID](#get-user-by-id)
    - [Get User By Username](#get-user-by-username)
    - [Get User By Email](#get-user-by-email)
    - [Modify User Username](#modify-user-username)
    - [Modify User Email](#modify-user-email)
    - [Modify User Profile](#modify-user-profile)
  - [Security](#security)
    - [Modify User Password](#modify-user-password)
    - [Get User Safety Records](#get-user-safety-records)

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
        "updatedAt": "string"
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
        "updatedAt": "string"
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

## User Management

### User Information & Profile

#### Get User By ID

- **Description**: Get user information by user ID.

- **Endpoint**: `GET /users/id/:id`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "_id": "string",
        "username": "string",
        "email": "string",
        "createdAt": "string",
        "updatedAt": "string",
        "profile": {
          "firstName": "string",
          "lastName": "string",
          "gender": null,
          "location": "string",
          "birthday": "string",
          "bio": "string",
          "avatar": "string",
          "customizeUrls": []
        },
        "experience": 0,
        "identity": []
      },
      "message": "User found successfully."
    }
    ```

    > The `gender` field is `null` if not specified, `true` is male, and `false` is female.

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

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND",
        "details": {
          "userId": "string"
        }
      }
    }
    ```

#### Get User By Username

- **Description**: Get user information by username.

- **Endpoint**: `GET /users/username/:username`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "_id": "string",
        "username": "string",
        "email": "string",
        "createdAt": "string",
        "updatedAt": "string",
        "profile": {
          "firstName": "string",
          "lastName": "string",
          "gender": null,
          "location": "string",
          "birthday": "string",
          "bio": "string",
          "avatar": "string",
          "customizeUrls": []
        },
        "experience": 0,
        "identity": []
      },
      "message": "User found successfully."
    }
    ```

    > The `gender` field is `null` if not specified, `true` is male, and `false` is female.

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username is required",
      "error": {
        "code": "MISSING_USERNAME",
        "details": {
          "username": "string"
        }
      }
    }
    ```

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND",
        "details": {
          "username": "string"
        }
      }
    }
    ```

#### Get User By Email

- **Description**: Get user information by email.

- **Endpoint**: `GET /users/email/:email`

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "_id": "string",
        "username": "string",
        "email": "string",
        "createdAt": "string",
        "updatedAt": "string",
        "profile": {
          "firstName": "string",
          "lastName": "string",
          "gender": null,
          "location": "string",
          "birthday": "string",
          "bio": "string",
          "avatar": "string",
          "customizeUrls": []
        },
        "experience": 0,
        "identity": []
      },
      "message": "User found successfully."
    }
    ```

    > The `gender` field is `null` if not specified, `true` is male, and `false` is female.

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

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND",
        "details": {
          "email": "string"
        }
      }
    }
    ```

#### Modify User Username

- **Description**: Modify the username of the user.

- **Endpoint**: `PUT /users/:id/username`

- **Request Body**:

  ```json
  {
    "username": "string"
  }
  ```

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "_id": "string",
        "username": "string",
        "email": "string",
        "createdAt": "string",
        "updatedAt": "string",
        "profile": {
          "firstName": "string",
          "lastName": "string",
          "gender": null,
          "location": "string",
          "birthday": "string",
          "bio": "string",
          "avatar": "string",
          "customizeUrls": []
        },
        "experience": 0,
        "identity": []
      },
      "message": "Username modified successfully."
    }
    ```

    > The `gender` field is `null` if not specified, `true` is male, and `false` is female.

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username is required",
      "error": {
        "code": "MISSING_USERNAME",
        "details": {
          "username": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username must be at least 3 characters long",
      "error": {
        "code": "USERNAME_TOO_SHORT",
        "details": {
          "username": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username must be at most 20 characters long",
      "error": {
        "code": "USERNAME_TOO_LONG",
        "details": {
          "username": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username must contain only letters, numbers, underscores, and hyphens",
      "error": {
        "code": "INVALID_USERNAME",
        "details": {
          "username": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Username already in use",
      "error": {
        "code": "USERNAME_IN_USE",
        "details": {
          "username": "string"
        }
      }
    }
    ```

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND",
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

#### Modify User Email

- **Description**: Modify the email of the user.

- **Endpoint**: `PUT /users/:id/email`

- **Request Body**:

  ```json
  {
    "email": "string",
    "code": 123456
  }
  ```

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "_id": "string",
        "username": "string",
        "email": "string",
        "createdAt": "string",
        "updatedAt": "string",
        "profile": {
          "firstName": "string",
          "lastName": "string",
          "gender": null,
          "location": "string",
          "birthday": "string",
          "bio": "string",
          "avatar": "string",
          "customizeUrls": []
        },
        "experience": 0,
        "identity": []
      },
      "message": "Email modified successfully."
    }
    ```

    > The `gender` field is `null` if not specified, `true` is male, and `false` is female.

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

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND",
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

> Please get a verification code before attempting to modify the email. Use the [`Get Email Verification Code`](#get-email-verification-code) endpoint.

#### Modify User Profile

- **Description**: Modify the profile of the user.

- **Endpoint**: `PUT /users/:id/profile`

- **Request Body**:

  ```json
  {
    "firstName": "string",
    "lastName": "string",
    "gender": null,
    "location": "string",
    "birthday": "string",
    "bio": "string",
    "avatar": "string",
    "customizeUrls": []
  }
  ```

  > The `firstName`, `lastName`, `location`, `bio`, `avatar`, and `customizeUrls` fields are optional.</br>
  > The `gender` field is `null` if not specified, `true` is male, and `false` is female.</br>
  > The `avatar` field is a base64 encoded image. (size limit: 5MB)

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": {
        "_id": "string",
        "username": "string",
        "email": "string",
        "createdAt": "string",
        "updatedAt": "string",
        "profile": {
          "firstName": "string",
          "lastName": "string",
          "gender": null,
          "location": "string",
          "birthday": "string",
          "bio": "string",
          "avatar": "string",
          "customizeUrls": []
        },
        "experience": 0,
        "identity": []
      },
      "message": "Profile modified successfully."
    }
    ```

    > The `gender` field is `null` if not specified, `true` is male, and `false` is female.

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "First name is too long",
      "error": {
        "code": "FIRST_NAME_TOO_LONG"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Last name is too long",
      "error": {
        "code": "LAST_NAME_TOO_LONG"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Gender is invalid",
      "error": {
        "code": "GENDER_INVALID"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Location is too long",
      "error": {
        "code": "LOCATION_TOO_LONG"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Birthday is invalid",
      "error": {
        "code": "BIRTHDAY_INVALID"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Bio is too long",
      "error": {
        "code": "BIO_TOO_LONG"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Avatar is invalid",
      "error": {
        "code": "AVATAR_INVALID"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Avatar is too large",
      "error": {
        "code": "AVATAR_TOO_LARGE"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Customize URLs is invalid",
      "error": {
        "code": "CUSTOMIZE_URLS_INVALID"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Customize URLs are too long",
      "error": {
        "code": "CUSTOMIZE_URLS_TOO_LONG"
      }
    }
    ```

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND",
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

### Security

#### Modify User Password

- **Description**: Modify the password of the user.

- **Endpoint**: `PUT /users/:id/password`

- **Request Body**:

  ```json
  {
    "currentPassword": "string",
    "newPassword": "string"
  }
  ```

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "message": "Password modified successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Current and new passwords are required",
      "error": {
        "code": "MISSING_PASSWORDS",
        "details": {
          "currentPassword": "string",
          "newPassword": "string"
        }
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "New password must be at least 6 characters long",
      "error": {
        "code": "PASSWORD_TOO_SHORT"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "New password must be different from the current password",
      "error": {
        "code": "PASSWORD_SAME_AS_CURRENT"
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
      "message": "Incorrect password",
      "error": {
        "code": "INCORRECT_PASSWORD"
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

#### Get User Safety Records

- **Description**: Get the safety records of the user.

- **Endpoint**: `GET /users/safety-records/:id`

- **Request Query**:

  - `startIndex`: The start index of the safety records.
  - `limit`: The number of safety records to return.

- **Response**:

  - **Success**: `200 OK`

    ```json
    {
      "status": "success",
      "data": [
        {
          "type": "string",
          "operationTime": "string",
          "ipAddress": "string",
          "device": "string"
        }
      ],
      "message": "Safety records found successfully."
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Start index is invalid",
      "error": {
        "code": "START_INDEX_INVALID"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Limit is invalid",
      "error": {
        "code": "LIMIT_INVALID"
      }
    }
    ```

  - **Error**: `400 Bad Request`

    ```json
    {
      "status": "error",
      "message": "Limit is too large",
      "error": {
        "code": "LIMIT_TOO_LARGE"
      }
    }
    ```

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "Safety records not found",
      "error": {
        "code": "SAFETY_RECORDS_NOT_FOUND",
        "details": {
          "userId": "string"
        }
      }
    }
    ```

  - **Error**: `404 Not Found`

    ```json
    {
      "status": "error",
      "message": "User not found or no safety records found",
      "error": {
        "code": "RECORDS_NOT_FOUND",
        "details": {
          "userId": "string"
        }
      }
    }
    ```

  - **Error**: `500 Internal Server Error`

    ```json
    {
      "status": "error",
      "message": "Error getting safety records",
      "error": {
        "code": "GET_ERROR"
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

> Safety records are automatically generated when the user logs in, 2FA, and changes their password.</br>
> Safety records will only show the last 100 records.
