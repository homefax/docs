# HomeFax API Documentation

This document provides an overview of the HomeFax API endpoints, request/response formats, and authentication methods.

## Base URL

```
http://localhost:3001
```

For production:

```
https://api.homefax.xyz
```

## Authentication

The API uses JWT (JSON Web Token) for authentication. Include the token in the Authorization header:

```
Authorization: Bearer <your_jwt_token>
```

### Getting a Token

You can obtain a JWT token by:

1. Registering a new user
2. Logging in with email and password
3. Connecting a crypto wallet

## API Endpoints

### Authentication

#### Register a new user

```
POST /auth/register
```

Request body:

```json
{
  "email": "user@example.com",
  "name": "John Doe",
  "password": "securePassword123",
  "walletAddress": "0x123abc..." // Optional
}
```

Response:

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com",
    "name": "John Doe",
    "walletAddress": "0x123abc...",
    "isEmailVerified": false,
    "isWalletVerified": false,
    "createdAt": "2025-02-26T08:14:29.000Z",
    "updatedAt": "2025-02-26T08:14:29.000Z"
  }
}
```

#### Login with email and password

```
POST /auth/login
```

Request body:

```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

Response: Same as register endpoint

#### Login with wallet

```
POST /auth/wallet
```

Request body:

```json
{
  "walletAddress": "0x123abc..."
}
```

Response: Same as register endpoint

#### Get user profile

```
GET /auth/profile
```

Response:

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "email": "user@example.com",
  "name": "John Doe",
  "walletAddress": "0x123abc...",
  "isEmailVerified": false,
  "isWalletVerified": false,
  "createdAt": "2025-02-26T08:14:29.000Z",
  "updatedAt": "2025-02-26T08:14:29.000Z"
}
```

### Users

#### Get all users (Admin only)

```
GET /users
```

Response:

```json
[
  {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com",
    "name": "John Doe",
    "walletAddress": "0x123abc...",
    "isEmailVerified": false,
    "isWalletVerified": false,
    "createdAt": "2025-02-26T08:14:29.000Z",
    "updatedAt": "2025-02-26T08:14:29.000Z"
  }
  // More users...
]
```

#### Get user by ID

```
GET /users/:id
```

Response: Same as user object above

#### Update user

```
PATCH /users/:id
```

Request body:

```json
{
  "name": "Updated Name",
  "email": "newemail@example.com"
}
```

Response: Updated user object

#### Delete user

```
DELETE /users/:id
```

Response: Status 200 OK

### Properties

#### Create property

```
POST /properties
```

Request body:

```json
{
  "address": "123 Blockchain Street",
  "city": "Crypto City",
  "state": "CA",
  "zipCode": "94105",
  "description": "Beautiful home in a quiet neighborhood"
}
```

Response:

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "address": "123 Blockchain Street",
  "city": "Crypto City",
  "state": "CA",
  "zipCode": "94105",
  "description": "Beautiful home in a quiet neighborhood",
  "owner": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com"
  },
  "isVerified": false,
  "transactionHash": "0x123abc...",
  "createdAt": "2025-02-26T08:14:29.000Z",
  "updatedAt": "2025-02-26T08:14:29.000Z"
}
```

#### Get all properties

```
GET /properties
```

Query parameters:

- `page`: Page number (default: 1)
- `limit`: Items per page (default: 10)
- `search`: Search term for address, city, etc.
- `state`: Filter by state
- `verified`: Filter by verification status (true/false)

Response:

```json
{
  "items": [
    // Property objects
  ],
  "meta": {
    "totalItems": 100,
    "itemCount": 10,
    "itemsPerPage": 10,
    "totalPages": 10,
    "currentPage": 1
  }
}
```

#### Get property by ID

```
GET /properties/:id
```

Response: Property object with reports

#### Update property

```
PATCH /properties/:id
```

Request body:

```json
{
  "address": "456 Updated Street",
  "description": "Updated description"
}
```

Response: Updated property object

#### Verify property (Admin only)

```
PATCH /properties/:id/verify
```

Response: Updated property object with isVerified = true

### Reports

#### Create report

```
POST /reports
```

Request body:

```json
{
  "propertyId": "550e8400-e29b-41d4-a716-446655440000",
  "reportType": "inspection",
  "title": "Home Inspection Report",
  "description": "Comprehensive inspection of the property",
  "content": "Base64 encoded file or JSON data",
  "price": 49.99
}
```

Response:

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "title": "Home Inspection Report",
  "reportType": "inspection",
  "description": "Comprehensive inspection of the property",
  "property": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "address": "123 Blockchain Street"
  },
  "creator": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com"
  },
  "price": 49.99,
  "contentHash": "QmXzYgaP5L9Eqhwgy5ygEwNnfPkqvVeHsAjWBfM4E1KHEv",
  "isVerified": false,
  "transactionHash": "0x123abc...",
  "createdAt": "2025-02-26T08:14:29.000Z"
}
```

#### Get all reports

```
GET /reports
```

Query parameters:

- `page`: Page number (default: 1)
- `limit`: Items per page (default: 10)
- `propertyId`: Filter by property ID
- `reportType`: Filter by report type
- `verified`: Filter by verification status (true/false)

Response:

```json
{
  "items": [
    // Report objects
  ],
  "meta": {
    "totalItems": 100,
    "itemCount": 10,
    "itemsPerPage": 10,
    "totalPages": 10,
    "currentPage": 1
  }
}
```

#### Get report by ID

```
GET /reports/:id
```

Response: Report object

#### Purchase report

```
POST /reports/:id/purchase
```

Request body:

```json
{
  "paymentMethod": "crypto", // or "credit"
  "transactionHash": "0x123abc..." // Required for crypto payments
}
```

Response:

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "report": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "title": "Home Inspection Report"
  },
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com"
  },
  "paymentMethod": "crypto",
  "transactionHash": "0x123abc...",
  "amount": 49.99,
  "createdAt": "2025-02-26T08:14:29.000Z"
}
```

#### Get report content

```
GET /reports/:id/content
```

Response:

```json
{
  "content": "Base64 encoded file or JSON data",
  "contentType": "application/pdf"
}
```

#### Verify report (Admin only)

```
PATCH /reports/:id/verify
```

Response: Updated report object with isVerified = true

## Error Responses

All API errors follow this format:

```json
{
  "statusCode": 400,
  "message": "Error message here",
  "error": "Bad Request"
}
```

Common status codes:

- 400: Bad Request
- 401: Unauthorized
- 403: Forbidden
- 404: Not Found
- 409: Conflict
- 500: Internal Server Error

## Rate Limiting

The API implements rate limiting to prevent abuse. Limits are:

- 100 requests per minute for authenticated users
- 20 requests per minute for unauthenticated users

When rate limited, you'll receive a 429 Too Many Requests response.

## Swagger Documentation

A more detailed API documentation is available at:

```
/api/docs
```

This interactive documentation allows you to test the API endpoints directly from your browser.
