# Public API Documentation

This document covers only publicly accessible API endpoints (excluding admin-only and internal endpoints).

## Base URL and Versioning

- Base URL: `https://iot.pestscan.eu`
- API version: `v1.0`
- Route prefix: `/api/v{version}`
- Swagger UI: `/swagger`

Base path:

```text
https://iot.pestscan.eu/api/v1.0
```

## Authentication

Authentication uses JWT bearer tokens.

- Send access token in header:

```http
Authorization: Bearer <access_token>
```

- Token flow:

1. Call `POST /Authentication/Login`
2. Use `body.accessToken` for protected endpoints
3. Refresh with `POST /Authentication/Refresh` when needed

## Public Endpoints

## 1) Login

- Method: `POST`
- Path: `/api/v1.0/Authentication/Login`
- Auth required: `No`

Request body:

```json
{
	"message_id": "string",
	"date": "2026-03-11T13:39:00.943Z",
	"body": {
		"email": "user@example.com",
		"password": "string"
	}
}
```

Success response `200`:

```json
{
	"message_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
	"date": "2026-03-11T13:39:00.960Z",
	"body": {
		"accessToken": "string",
		"refreshToken": "string"
	},
	"is_successful": true
}
```

Common error `400`:

```json
{
	"message_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
	"date": "2026-03-11T13:39:00.962Z",
	"body": "string",
	"is_successful": true,
	"error": {
		"message": "string",
		"code": "string",
		"details": "string"
	}
}
```

Notes:

- Email is normalized to lowercase on login.
- Unverified users are rejected with code `2`.

## 2) Refresh Token

- Method: `POST`
- Path: `/api/v1.0/Authentication/Refresh`
- Auth required: `No` (requires valid email + refresh token pair)

Request body:

```json
{
	"message_id": "string",
	"date": "2026-03-11T13:39:00.943Z",
	"body": {
		"email": "user@example.com",
		"password": "string"
	}
}
```

Success response `200`:

```json
{
	"message_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
	"date": "2026-03-11T13:40:53.404Z",
	"body": {
		"accessToken": "string",
		"refreshToken": "string"
	},
	"is_successful": true
}
```

Common error `400`:

```json
{
	"message_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
	"date": "2026-03-11T13:40:53.405Z",
	"body": "string",
	"is_successful": true,
	"error": {
		"message": "string",
		"code": "string",
		"details": "string"
	}
}
```

## 3) Create Event(s)

- Method: `POST`
- Path: `/api/v1.0/Event`
- Auth required: `Yes`
- Required header: `pco-id: <string>`

Headers:

```http
Authorization: Bearer <access_token>
pco-id: 3a2f0c4e-5905-4f71-b8ee-a91f1c412345
Content-Type: application/json
```

Required vs optional:

- Required headers: `Authorization`, `pco-id`
- Required body fields (minimum accepted by current backend): `body`, `body.events`
- Required root fields: `message_id`
- Required fields per event: `event_id`, `duid`, `event_type`
- Optional root fields: `date`
- Optional per-event fields: `vendor_code`, `date`, `status`, `battery_level`, `signal_intensity`, `location_id`, `message`, `attachments`, `common_attributes`, `unknown_attributes`

Minimal request body:

```json
{
	"message_id": "8890fdf8-e31d-4c6a-80d0-84fee8b1627d",
	"body": {
		"events": [
			{
				"event_id": "evt-001",
				"duid": "device-123",
				"event_type": 901
			}
		]
	}
}
```

Full request body example:

```json
{
	"message_id": "c6d8e843-9789-4d8c-af18-3fe4fb0ef30f",
	"date": "2026-02-19T13:45:00Z",
	"body": {
		"events": [
			{
				"event_id": "evt-001",
				"duid": "device-123",
				"vendor_code": "vendor-a",
				"date": "2026-02-19T13:40:00Z",
				"status": 1,
				"battery_level": 91.5,
				"signal_intensity": "-68dBm",
				"event_type": 901,
				"location_id": "loc-22",
				"message": "Heartbeat",
				"attachments": [
					{
						"mime_type": "image/jpeg",
						"base64": "<base64-data>"
					}
				],
				"common_attributes": {
					"temprature": 21.6,
					"temprature_uom": 1,
					"molecules": [
						{
							"molecule_id": 10,
							"molecule_value": 22,
							"molecule_uom": 1
						}
					],
					"species_found": [
						{
							"quantity": 2,
							"species_id": 7
						}
					]
				},
				"unknown_attributes": {
					"any_key": "any_value"
				}
			}
		]
	}
}
```

Success response `200`:

```json
{
	"message_id": "...",
	"date": "...",
	"body": {
		"message": "Event(s) stored.",
		"event": {
			"message_id": "...",
			"date": "...",
			"body": {
				"events": [
					{
						"event_id": "evt-001"
					}
				]
			}
		}
	},
	"is_successful": true
}
```

Common errors `400`:

- Missing header `pco-id` (code `1`)
- Duplicate `event_id` already exists (code `2`)
- Partial forwarding issue still returns `400` with code `200` and indicates retry behavior

## Event Type Values

- `0` Unknown
- `100` TrapFired
- `101` TrapFiredWithCatch
- `102` TrapFiredWithoutCatch
- `103` TrapSetReady
- `104` TrapServiced
- `105` TrapFiredIncorrectly
- `200` Motion
- `201` HeavyMotion
- `202` Vibration
- `900` ConnectionInterrupted
- `901` Heartbeat
- `902` HeartbeatFailed
