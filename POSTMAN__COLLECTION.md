# PestScan PC40 – Postman Collection Documentation

This Postman collection is provided as a **reference implementation** for integrating with the PestScan PC40 API.

It allows integrators and vendors to:

- Authenticate against the PestScan API
- Refresh authentication tokens
- Send PC40 event messages
- Validate payloads before building a production integration

---

# 🔗 Public Postman Collection

Import the collection using the link below:

https://pestscandevelopment.postman.co/workspace/PestScan~77a3dbc9-cd3c-4782-8f90-d86f23b7657d/collection/26400433-f93f002d-cfea-4b3f-bbdf-f7bdf7144f04?action=share&creator=26400433

---

# 1. Importing the Collection

1. Open **Postman**
2. Click **Import**
3. Paste the public link above
4. Fork or import it into your workspace

It is strongly recommended to create a dedicated **Environment** before using the collection.

---

# 2. Create a Postman Environment (Required)

Before running any request, you MUST configure environment variables.

Go to:

Postman → Environments → Create New

Example environment name:

PestScan PC40 - Development

---

# 3. Required Environment Variables

You must fill in the following variables before using the collection.

If these are not configured, the requests will fail.

| Variable | Required | Description |
|----------|----------|-------------|
| baseUrl | Yes | API base URL (e.g. https://iot.pestscan.eu) |
| apiVersion | Yes | API version (e.g. v1.0) |
| email | Yes | Your PestScan login email |
| password | Yes | Your PestScan password |
| accessToken | Yes (after login) | Bearer token returned from Login |
| refreshToken | Yes (after login) | Refresh token returned from Login |
| pco-id | Yes (for Event endpoint) | PCO-ID created in PestScan Permanent Monitoring |

---

## ⚠ Important

- accessToken is required for all protected endpoints.
- `pco-id` MUST be included in the request header when sending events.
- If variables are not filled in, the collection will not work.

---

# 4. API Base Structure

All endpoints follow this format:

{{baseUrl}}/api/{{apiVersion}}/{Endpoint}

Example:

https://iot.pestscan.eu/api/v1.0/Authentication/Login

---

# 5. Authentication Flow

## Step 1 – Login

Endpoint:

POST {{baseUrl}}/api/{{apiVersion}}/Authentication/Login

Body:
```json
{
  "email": "{{email}}",
  "password": "{{password}}"
}
```
Success Response:

The API returns:

- accessToken
- refreshToken

You must store these in your Postman environment variables.

---

## Step 2 – Refresh Token

When the access token expires, use:

POST {{baseUrl}}/api/{{apiVersion}}/Authentication/Refresh

Body:
```json
{
  "email": "{{email}}",
  "jwt": {
    "accessToken": "{{accessToken}}",
    "refreshToken": "{{refreshToken}}"
  }
}
```
This returns a new access token and refresh token.

---

# 6. Sending PC40 Events

Endpoint:

POST {{baseUrl}}/api/{{apiVersion}}/Event

Required Headers:

Authorization: Bearer {{accessToken}}
pco-id: {{pco-id}}
Content-Type: application/json

If the `pco-id` header is missing, the request will be rejected.

---

## Minimal Event Payload Example
```json
{
  "message_id": "unique-message-id",
  "body": {
    "events": [
      {
        "event_id": "event-001",
        "duid": "device-123",
        "event_type": 901
      }
    ]
  }
}
```
Minimum required fields:

Root level:
- message_id

Inside body.events:
- event_id
- duid
- event_type

---

# 7. Event Type Reference

| Value | Meaning |
|--------|---------|
| 100 | TrapFired |
| 101 | TrapFiredWithCatch |
| 102 | TrapFiredWithoutCatch |
| 103 | TrapSetReady |
| 104 | TrapServiced |
| 105 | TrapFiredIncorrectly |
| 200 | Motion |
| 201 | HeavyMotion |
| 202 | Vibration |
| 900 | ConnectionInterrupted |
| 901 | Heartbeat |
| 902 | HeartbeatFailed |

---

# 8. PestScan Platform Requirements

Before sending events to production, ensure:

1. Permanent Monitoring module is enabled.
2. A PC40 Profile has been created.
3. You have copied the correct PCO-ID.
4. Permanent Monitoring is enabled on the customer.
5. Locations are configured correctly.

If location_id is not provided in the payload, traps can later be assigned manually using Trap Assignment in PestScan.

---

# 9. Recommended Testing Order

1. Run Login
2. Verify tokens are stored
3. Send a Heartbeat event (event_type 901)
4. Confirm the event appears in PestScan
5. Test other event types

---

# Production Integration Notes

This Postman collection is intended for:

- Testing
- Debugging
- Reference during development

It should NOT be used as a production integration layer.

Production systems should implement:

- Secure credential storage
- Automatic token refresh handling
- Retry mechanisms
- Logging and monitoring
- Proper error handling

---

# Summary

This collection provides a complete reference implementation of:

- Authentication
- Token refresh
- PC40 event submission

Make sure all required variables are configured before running requests.
