# EasySendSMS REST API (v1)

## Overview

The [BulkSMSOnline REST API](https://www.bulksmsonline.com/) gives you a complete toolkit for embedding messaging and verification into your software. It goes far beyond simple SMS sending and receiving, you also get HLR lookups for real-time number status, number validation, and a range of additional utilities. Every endpoint is built with accuracy and uptime in mind, so you can confidently add enterprise-grade communication features to your applications.


---

## Code Examples

BulkSMSOnline ships with **complete, copy-paste code samples in multiple languages** so you can start integrating without guesswork. Each example covers the full workflow authentication, sending an SMS, and handling the server response with step‑by‑step setup guidance built right in.


### Quick Start

Clone the repository and run your preferred language example:

```bash
# 1. Clone the repository
git clone https://github.com/bulksmsonline26/REST-API-V1.git

# 2. Navigate to an example directory (e.g., Python)
cd REST-API-v1/examples/python

# 3. Follow the instructions in the example's README.md file
pip install requests
export BULKSMS_USERNAME='your_actual_username'
export BULKSMS_PASSWORD='your_actual_password'
python send_sms.py
```



---

## Base URL

The base URL for all API endpoints is:

```
https://api.bulksmsonline.co/
```

| Endpoint | Full URL |
| --- | --- |
| Send SMS | `https://api.bulksmsonline.co/rest/api/v1/sms/send/` |
| SMS Balance | `https://api.bulksmsonline.co/balance` |
| MNP Query | `https://api.bulksmsonline.co/mnp` |
| Number Validation (NV) | `https://api.bulksmsonline.co/mnv` |

---

## Request Headers

When making a request, include the following headers:

| Header | Value |
| --- | --- |
| `Content-Type` | `application/json` |
| `Accept` | `application/json` |

---

## Rate Limits

To ensure a high quality of service for all customers, the BulkSMSOnline API applies rate limits across all endpoints. If you exceed the rate limit, the API will return an HTTP `429 Too Many Requests` status code.

| Endpoint | Default Rate Limit | Maximum Rate Limit |
| --- | --- | --- |
| Send SMS | 30 requests/second per account | Up to 150 requests/second per IP (contact support) |
| SMS Balance | 2 requests/minute per account per IP | -- |
| HLR Query | 30 requests/second per account | Up to 150 requests/second per IP (contact support) |
| Number Validation (NV) | 30 requests/second per account | Up to 150 requests/second per IP (contact support) |

> For the SMS Balance endpoint, you can retry the request after 60 seconds if rate limited.

---

# Endpoints

---

## 1. Send SMS

The Send SMS API gives you a powerful, flexible way to embed SMS messaging directly into your apps, sites, or services. With just a handful of lines, you can send text messages to mobile phones worldwide, making it a go‑to tool for any business that wants to improve customer, employee, or user communications.

#### Why Use the Send SMS API?

- **Worldwide Coverage**: Reach recipients in any country, so your messages land wherever your audience is located.

- **Instant Transmission**: Send urgent alerts, notifications, and updates in real time, perfect for time‑critical communication.

- **Elastic Scale**: From one message to millions, the API adapts automatically to your volume without performance drops.

- **Tailored Messaging**: Customise sender IDs, message text, and use Unicode to support multiple languages and character sets.

- **Dependable Delivery**: Rely on a resilient infrastructure and consistently high deliverability to get your messages through.

### Endpoint

```
POST rest/api/v1/sms/send/
```

### Request Headers

| Header | Value |
| --- | --- |
| `token` | `YOUR_GENERATED_REST_TOKEN` |
| `Content-Type` | `application/json` |
| `Accept` | `application/json` |

### Request Body Parameters

All requests to the BulkSMSOnline API should use the `application/json` content type and the `POST` method. Ensure that the request body is in JSON format and that you use raw JSON data in your POST requests. We do not support the `GET` method. All data must be sent in the request body as JSON when using the `POST` method.

| Parameter | Description | Presence |
|-----------|-------------|----------|
| `from` | Sender Name that the message will appear from. Max Length of 15 if numeric. Max Length of 11 if alphanumeric. To prefix the plus sign ("+") to the sender's address when the message is displayed on their cell phone, please prefix the plus sign to your sender's address while submitting the message (note the plus sign should be URL encoded). Additional restrictions on this field may be enforced by the SMSC. | Required |
| `to` | Mobile number of the recipient that the message will be sent to, e.g., 19876543210 (Do not use + or 00 before the country code). You can use a comma in the `to` parameter to send to multiple numbers, with a maximum of 30 numbers in each request. | Required |
| `content` | The message to be sent. It can be English as plain text or any other language as Unicode, max message length 5 parts. For concatenated (long) messages, every 153 characters are counted as one message for plain text and 67 characters for Unicode, as the rest of the characters will be used by the system for packing extra information for re-assembling the message on the cell phone. | Required |
| `type` | Indicates the type of message. Values for type include: 0: Plain text (GSM 3.38 Character encoding) 1: Unicode (For any other language) | Required |
| `sendDateTime` | The scheduled date and time for sending the message, formatted in ISO 8601. Example: '2023-12-31T19:35:00'. The time zone used is UTC, so please ensure that the date and time are provided according to UTC. | Optional |

#### Message Type Support

The [BulkSMSOnline REST API](https://bulksmsonline.com) supports various message types and can handle multiple recipients in a single request, ensuring your messages are delivered promptly and reliably.

#### Bulk SMS Sending

- **Strict error handling**: If any error other than "Invalid mobile number" (4012) appears for one of the numbers, the whole batch request is cancelled instantly.

- **Soft skip for invalid numbers**: Only the 4012 error (invalid mobile number) triggers a skip, that number is removed from the batch and the rest are processed normally.

- **Size limit**: You can send up to 30 numbers in a single request.

- **Duplicate handling**: Identical numbers are ignored; only unique entries are kept.

## SMS Send API Examples

Below are example requests and responses for sending SMS messages using the BulkSMSOnline REST API.

---

### Example 1: Standard Text Message (Single Recipient)

This example shows how to send a standard plain text message to a single mobile number.

#### Request

```bash
curl -X POST "https://api.bulksmsonline.co/rest/api/v1/sms/send/" \
  -H "token: YOUR_GENERATED_REST_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "from": "YourBrand",
    "to": [
      "1234567890",
      "1234567891",
      "1234567892"
    ],
    "type": "Text",
    "content": "Your SMS message",
    "sendDateTime": null
  }'
```

#### Success Response

```json
{
    "status": "OK",
    "scheduled": "Now",
    "messageIds": [
        "OK: 69991a73-a560-429f-9c5a-3251dc1522bb"
    ]
}
```

---

### Example 2: Unicode Message (Arabic)

This example demonstrates how to send a message containing Unicode characters, such as Arabic. Note that the `type` parameter is set to `1`.

#### Request

```bash
curl -X POST "https://api.bulksmsonline.co/rest/api/v1/sms/send/" \
  -H "token: YOUR_GENERATED_REST_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "from": "YourBrand",
    "to": [
      "1234567890",
      "1234567891",
      "1234567892"
    ],
    "type": "1",
    "content": "هذه رسالة اختبار",
    "sendDateTime": null
  }'
```

#### Success Response

```json
{
    "status": "OK",
    "scheduled": "Now",
    "messageIds": [
        "OK: 8f3d9b7a-5c1e-4b9a-8d2f-1c7e9a0b3d1c"
    ]
}
```

---

### Example 3: Scheduled Message

This example shows how to schedule an SMS to be sent at a future date and time. The `scheduled` parameter must be a valid ISO 8601 timestamp in UTC.

#### Request

```bash
curl -X POST "https://api.bulksmsonline.co/rest/api/v1/sms/send/" \
-H "token: YOUR_GENERATED_REST_TOKEN" \
-H "Content-Type: application/json" \
-H "Accept: application/json" \
-d '{
    "from": "SenderName",
    "to": "12345678900",
    "content": "This is a scheduled message for the team meeting.",
    "type": "0",
    "sendDateTime": "2026-03-15T10:00:00Z"
}' \
```

#### Success Response

```json
{
    "status": "OK",
    "scheduled": "2026-03-15T10:00:00Z",
    "messageIds": [
        "OK: a1b2c3d4-e5f6-7890-1234-567890abcdef"
    ]
}
```

---

