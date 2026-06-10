# BulkSMSOnline APIs (v1)

## Overview

The [BulkSMSOnline REST API](https://www.bulksmsonline.com/) gives you a complete toolkit for embedding messaging and verification into your software. It goes far beyond simple SMS sending and receiving, you also get HLR lookups for real-time number status, number validation, and a range of additional utilities. Every endpoint is built with accuracy and uptime in mind, so you can confidently add enterprise-grade communication features to your applications.


---

## Code Examples

BulkSMSOnline ships with **complete, copy-paste code samples in multiple languages** so you can start integrating without guesswork. Each example covers the full workflow authentication, sending an SMS, and handling the server response with step‑by‑step setup guidance built right in.


### Quick Start

Clone the repository and run your preferred language example:

```bash
# 1. Clone the repository
git clone https://github.com/bulksmsonline26/BulkSMSOnline_APIs.git

# 2. Navigate to the PHP example directory
cd BulkSMSOnline_APIs/Examples/HTTP SMS API/PHP

# 3. Follow the instructions in the example's README.md file

# If the project uses Composer, install dependencies first:
composer install

# Set your BulkSMSOnline credentials as environment variables
export BULKSMS_USERNAME='your_actual_username'
export BULKSMS_PASSWORD='your_actual_password'

# Run the PHP script
php send_sms.php
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

## 1. Generate REST API Token

Generate a temporary REST API token using your BulkSMSOnline username and password. Use this token when calling the REST API by passing it in the `token` HTTP header.

```bash
GET https://api.bulksmsonline.co/rest/api/v1/sms/gettoken/username/{YourUsername}/password/{YourPassword}
```

|Parameter	|Location	|Required	|Description|
|-----------|---------|---------|-----------|
|username | Path | Yes | Your account username or registered mobile number|
|password | Path | Yes | Your account password|

### Successful Token Response

```json
{
  "token": "YOUR_GENERATED_REST_TOKEN"
}
```

### Generate Code Response Codes

The token endpoint may return the following HTTP status codes.

|HTTP Status	|Code	|Meaning	|Description|
|-------------|-----|-------------|----------------------------|
| 200 | `ok` | Success | Returns the newly generated REST API token|
| 401 | `Unauthorized` | Unauthorized | The provided username or password is incorrect |
| 402  | `Payment Required` | Payment Required | The account has zero balance or the account/service has expired |
| 402  | `Payment Required` | Forbidden / Invalid Action | The request contains an invalid action or the account is not allowed to perform the action |
| 404 | `Not Found` | 	Invalid Path | The requested endpoint path is invalid |
| 405 | `Method Not Allowed` | Wrong Method | The endpoint was called using an unsupported HTTP method|
| 500 | `Internal Server Error` | Server Error | An unexpected server-side error occurred|
| 503 | `Service Unavailable` | Service unavailable | The service is temporarily unavailable |

---

## 2. Send SMS

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

### Example 4: Bulk Sending (Multiple Recipients)

This example demonstrates broadcasting one message to up to 30 recipients in a single API call. Simply pass a comma‑separated list of phone numbers to the ` to ` parameter.

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
        "OK: 1e1a7c30-a160-429f-9c5a-3251dc1522cc",
        "OK: 2f2b8d41-b271-430a-ad6b-4362ed2633dd",
        "OK: 3g3c9e52-c382-441b-be7c-5473fe3744ee"
    ]
}
```

---

### Send SMS Response Codes

The send SMS endpoint may return a response containing a `code` value. The following response codes are used to indicate whether the SMS request was accepted or rejected.

|HTTP Status	|Code	|Description	|Recommended Developer Action|
|-------------|-----|-------------|----------------------------|
| 200 | `Success` | SMS request was accepted successfully | No action required. Store the API response for tracking and audit purposes|
| 400 | `Bad Request` | Invalid parameters were provided in the request | Check the JSON body, required fields, destination numbers, sender ID, and message type|
| 401 | `Unauthorized` | The REST token is missing, invalid, or expired | Generate a new token and send it using the `token` HTTP header |
| 404 | `Not Found` | The endpoint path is invalid | Confirm that you are using the correct versioned endpoint URL |
| 405 | | The HTTP method is not allowed | Use `POST` for sending SMS|
| 500 | | Internal server error | Retry later. If the issue continues, contact support with the request details|
| 503 | | Service unavailable | Retry after a short delay |

---

## 3. Account Balance API

Use the Account Balance API to check the available SMS balance for your account. The endpoint supports both **GET** and **POST**, but POST is recommended for better credential privacy.

### Endpoint 

```bash
https://api.bulksmsonline.co/balance?username=XXXX&password=YYYYY
```

### Post Example

```bash
curl -X POST https://api.bulksmsonline.co/balance \
                              -d "username=XXXX" \
                              -d "password=YYYYY"
```

|Parameter | Required | Method | Description|
|----------|----------|--------|------------|
| `username` |Yes | 	GET / POST | Your BulkSMSOnline account username|
| `password` | Yes | GET / POST | Your BulkSMSOnline account password|

### Response Format

The API returns a plain text response. A successful request returns the current account balance. Failed requests return a two-digit response code.

| Response | Status | Description|
|----------|--------|------------|
|`125.000` | `Success` | The account was authenticated and the current SMS balance was returned|
|`00`| `Missing Parameters` | The request must include both `username` and `password`|
|`01` | `Username Error` | The username parameter is empty|
|`02` | `Password Error` | The password parameter is empty|
|`03` | `Username Error` | The username length is invalid. Username must be less than 30 characters|
|`04` | `Password Error` | The password length is invalid. Password must be less than 20 characters|
|`05` | `Username Error` | The username contains invalid characters|
|`06` | `Password Error` | The password contains invalid characters|
|`07` | `Authentication Failed` | The username/password is incorrect, the account is not active, or the account is not allowed to use this API|
|`08` | `Invalid Request` | The same parameter was sent more than once or sent in both **GET** and **POST**|
|`99` | `System Error` | Temporary system error. The request could not be processed due to an internal server or database issue|

**Security note**: *We recommend using POST for this endpoint because it keeps credentials out of the URL, browser history, and most server/proxy access logs.*

---

## 4. MNP Lookup API

Use the MNP Lookup API to check mobile number portability, roaming status, network-level details, and HLR lookup information that can help improve routing decisions, database quality, and delivery cost control.

### Endpoint

The endpoint supports both `GET` and `POST`. POST is recommended because it keeps your credentials out of the URL, browser history, and most server/proxy logs.

```bash
https://api.bulksmsonline.co/mnp?username=XXXX&password=YYYYY&msisdn=12025550100
```

### POST Example

```bash
curl -X POST https://api.bulksmsonline.co/mnp \
  -d "username=XXXX" \
  -d "password=YYYYY" \
  -d "msisdn=12025550100"
```

### MNP Request Parameters

|Presence | Parameter | Method | Description |
|---------|-----------|--------|-------------|
|Mandatory | `username` | GET / POST | Your BulkSMSOnline account username|
|Mandatory | `password` | GET / POST | Your BulkSMSOnline account password|
|Mandatory | `msisdn` | GET / POST | Mobile number in international format without a leading `+` or leading `0`. The value must be numeric and between **9 and 15 digits**. *Example:* `12025550100`|

### MNP Success Response

*On success, the API returns a JSON object containing a results array. The result includes the submitted MSISDN, country, operator, number type, MCC/MNC, roaming status, provider status, provider error code/description, and portability status.*

#### Successful Response Example

```json
{
            "results": [
              {
                "msisdn": "12025550100",
                "country": "United States",
                "err_desc": "No Error",
                "operator": "Example Operator",
                "type": "mobile",
                "mccmnc": "310000",
                "is_roaming": "false",
                "err_code": "0",
                "status": "DELIVERED",
                "is_ported": "false"
              }
            ]
}
```

#### MNP Response Fields

|Field	|Description |
|-------|------------|
|`results`|	An array containing the MNP/HLR lookup result|
|`msisdn`|	The submitted mobile number returned by the lookup provider|
|`country`|	The country detected from the number issuing information|
|`err_desc`|	The provider error description, when returned by the lookup provider|
|`operator`|	The detected mobile network/operator name|
|`type`|	The number type returned by the lookup provider, for example mobile or other available type|
|`mccmnc`|	The combined Mobile Country Code and Mobile Network Code|
|`is_roaming`|	Indicates whether the number is reported as roaming by the lookup provider|
|`err_code`|	The provider error code returned with the lookup result|
|`status`|	The provider status returned for the lookup request|
|`is_ported`|	Indicates whether the number is reported as ported by the lookup provider|

### MNP Error Codes

|Response	|Status	|Description|
|---------|-------|-----------|
|`00`|	Missing Parameters|	No parameters were submitted|
|`01`|	Username Error|	The username parameter is missing or empty|
|`02`|	Password Error|	The password parameter is missing or empty|
|`03`|	MSISDN Error|	The MSISDN parameter is missing or empty|
|`04`|	Invalid MSISDN|	The MSISDN format is invalid. It must be numeric, must not start with `0`, and must be between **9 and 15 digits**|
|`05`|	Username Error|	The username length is invalid. Username must be less than `30` characters|
|`06`|	Password Error|	The password length is invalid. Password must be less than `20` characters|
|`07`|	Invalid Characters|	Invalid characters were detected in one or more request parameters|
|`08`|	Invalid Request|	A parameter was sent more than once or sent in both GET and POST|
|`09`|	Authentication Failed|	Authentication failed. The username/password is incorrect, the account is not active, the account is a demo account, or the account is not allowed to use this API|
|`10`|	Insufficient Credits|	The account does not have enough available balance to complete the MNP lookup|
|`12`|	Provider Error|	The MNP/HLR provider did not return a valid response, returned an empty response, or the provider response could not be processed|
|`99`|	System Error|	Temporary system error. The request could not be processed due to an internal server, database, reporting, or routing issue. Please retry later|

---

## 5. MNV Number Validation API

Use the MNV API to validate mobile numbers, improve contact database quality, identify invalid entries, and reduce wasted SMS attempts caused by incorrectly formatted or unsupported numbers.

### Endpoint

*The endpoint supports both **GET** and **POST**. POST is recommended because it keeps your credentials out of the URL, browser history, and most server/proxy logs.*

```bash
https://api.bulksmsonline.co/mnv?username=XXXX&password=YYYYY&msisdn=12025550100
```

```bash
curl -X POST https://api.bulksmsonline.co/mnv \
  -d "username=XXXX" \
  -d "password=YYYYY" \
  -d "msisdn=12025550100"
```

### MNV Request Parameters


|Presence | Parameter | Method | Description |
|---------|-----------|--------|-------------|
|Mandatory | `username` | GET / POST | Your BulkSMSOnline account username|
|Mandatory | `password` | GET / POST | Your BulkSMSOnline account password|
|Mandatory | `msisdn` | GET / POST | Mobile number in international format without a leading `+` or leading `0`. The value must be numeric and between **9 and 15 digits**. *Example:* `12025550100`|

### MNV Success Response

*On success, the API returns a JSON object containing the detected country, network, operator, MCC/MNC, number type, network type, and the submitted MSISDN.*

#### Successful Response Example

```json
{
            "Country": "United States",
            "ISO3166_2": "US",
            "CC": "1",
            "NetName": "Example Network",
            "MCC": "310",
            "MNC": "000",
            "OPERATOR": "Example Operator",
            "Type": "Mobile",
            "NetType": "GSM",
            "MSISDN": "12025550100"
}
```

#### MNV Response Fields

|Field	|Description |
|-------|------------|
|`Country`|	The detected country name for the submitted MSISDN|
|`ISO3166_2`|	The two-letter ISO country code|
|`CC`|	The country calling code|
|`NetName`|	The detected network name|
|`MCC`|	The Mobile Country Code|
|`MNC`|	The Mobile Network Code|
|`OPERATOR`|	The detected mobile operator name|
|`Type`|	The number type returned by the validation database|
|`NetType`|	The detected network type, when available|
|`MSISDN`|	The submitted mobile number|

### MNV Error Codes

Failed requests return a plain text response code. These codes help you identify whether the issue is related to request format, authentication, balance, number validation, or a temporary system error.

|Response	|Status	|Description|
|---------|-------|-----------|
|`00`|	Missing Parameters|	No parameters were submitted|
|`01`|	Username Error|	The username parameter is missing or empty|
|`02`|	Password Error|	The password parameter is missing or empty|
|`03`|	MSISDN Error|	The MSISDN parameter is missing or empty|
|`04`|	Invalid MSISDN|	The MSISDN format is invalid. It must be numeric, must not start with `0`, and must be between **9 and 15 digits**|
|`05`|	Username Error|	The username length is invalid. Username must be less than `30` characters|
|`06`|	Password Error|	The password length is invalid. Password must be less than `20` characters|
|`07`|	Invalid Characters|	Invalid characters were detected in one or more request parameters|
|`08`|	Invalid Request|	A parameter was sent more than once or sent in both GET and POST|
|`09`|	Authentication Failed|	Authentication failed. The username/password is incorrect, the account is not active, the account is a demo account, or the account is not allowed to use this API|
|`10`|	Insufficient Credits|	The account does not have enough available balance to complete the MNP lookup|
|`11`|  No MNV Data|	The number format is valid, but no MNV data was found for the submitted MSISDN|
|`99`|	System Error|	Temporary system error. The request could not be processed due to an internal server, database, reporting, or routing issue. Please retry later|

**Billing note:** *The MNV lookup cost is deducted only after the number format is accepted, the account is authenticated, sufficient credits are available, and MNV data is successfully found.*

---

## 6. HTTP SMS API

The HTTP API is the fastest way to send SMS messages using a simple GET or POST request. It is useful for legacy systems, direct server-side integrations, and lightweight notification workflows.

### Endpoint

```bash
https://api.bulksmsonline.co/smsapi?username=XXXX&password=YYYYY&type=t&to=0000000000&source=sender&message=YourText
```

### Parameters

|Presence | Parameter | Description |
|---------|-----------|-------------|
|Mandatory | `username` | Your BulkSMSOnline account username|
|Mandatory | `password` | Your BulkSMSOnline account password `Note: If the password contains special characters, spaces, symbols, or reserved URL characters, it must be URL-encoded before being sent via the API.`|
|Mandatory | `type` | Defines the SMS message type:  -  **`t`**: Plain text message for English/Latin content. The message must be URL-encoded and should use GSM 03.38 character encoding. -  **`u`**: Unicode message for languages such as Arabic, Chinese, or other non-Latin scripts.|
|Mandatory | `to` | One or more recipient phone numbers, separated by commas. Maximum **30 numbers** per request.|
|Mandatory | `source` | The sender ID/name that will appear as the SMS sender: **1** Numeric sender ID: maximum **18 digits**. **2** Alphanumeric sender ID: maximum **11 characters**. **3**  Additional restrictions may apply depending on the SMSC or destination network.|
|Mandatory | `message` | The SMS message body: **1**  For plain text messages, such as English or Latin-based content, the message must be URL-encoded and should use GSM 03.38 character encoding. **2** For Unicode messages, such as Arabic, Chinese, Korean, or other non-Latin languages, our API supports both UTF-16BE text and normal Unicode text. You can send the message normally without converting it to UTF-16BE, as long as the text is properly URL-encoded when sent through a URL/API request.|
|Optional | `scheduled` | Schedule the SMS to be sent at a future date and time:**1** The date/time must be provided in ISO 8601 format: *`yyyy-MM-ddTHH:mm:ss`* **2** The value must be URL-encoded when sent via API. **3** The scheduled time must be based on the **`UTC-04:00`** timezone.*Example*:`scheduled=2020-01-13T12:49:00`|

### HTTP API Response Codes

The HTTP SMS API returns a response code after each request. A successful request returns **`OK|<MESSAGE_ID>`**. Error responses return an error code that explains why the SMS request was rejected or could not be processed.

**Tip**`: Always store the returned message ID from successful submissions. It can be used later for delivery tracking, reporting, support checks, or matching delivery reports.`

|Response / Error Code	|Meaning	|Description	|Recommended Developer Action|
|-------------|-----|-------------|----------------------------|
| `OK-<MESSAGE_ID>` | Message accepted | The SMS request was submitted successfully. The API response includes a unique message ID | Save the returned message ID for delivery tracking, logs, and support reference|
| `E0002` | Invalid request | The request URL is invalid, or one or more required parameters are missing, empty, or sent in the wrong format | Check the request URL and make sure all required parameters are included correctly|
| `E0003` | Invalid username or password | The username or password parameter is missing, incorrect, or not formatted properly | Verify the API credentials. If the password contains special characters, URL-encode it before sending the request|
| `E0004` | Invalid message type | The *`type`* parameter is invalid. Supported values include `t` for plain text SMS and `u`for Unicode SMS. | Send a valid message type according to the message encoding you are using |
| `E0005` | Invalid message body | The SMS message text is empty, invalid, or not encoded correctly | Make sure the message body is not empty. Plain text messages should be URL-encoded. Unicode messages should be encoded in UTF-16BE|
| `E0006` |	Invalid recipient number |	The to parameter contains an invalid mobile number or unsupported number format.|	Use international MSISDN format without spaces or invalid characters. For multiple recipients, separate numbers with commas.|
| `E0007` |	Invalid sender ID	| The *`source`* parameter does not match the allowed sender ID format. Numeric sender IDs can contain up to 15 digits, while alphanumeric sender IDs can contain up to 11 characters.|	Check the sender ID format and confirm that the sender is allowed for the destination country, route, or SMSC.|
| `E0008` |	Authentication failed |	The API credentials are incorrect, inactive, or not allowed to use the requested API service.|	Verify the account username, password, account status, and API access permissions.|
| `E0010` |	Internal server error |	The request could not be processed because of a temporary system-side issue.|	Retry after a short time. If the issue continues, contact support with the request URL, timestamp, and account username.|
| `E0022` |	Insufficient balance	| The account does not have enough SMS credit to submit the message.|	Top up the account balance and retry the request.|
| `E0033 / HTTP 429` | Rate limit exceeded |	The API sending rate limit has been exceeded. The maximum allowed rate is **30 SMS submissions per second**.|	Reduce the sending speed, queue messages on your side, and retry after a short delay.|
|`E0044`|	Mobile network not supported	|The destination number belongs to a mobile network that is not currently supported by the selected route or service.|Check the destination network and contact support if you need coverage for this operator or country.|


### HTTP Examples

#### Plain Text SMS Example
*Use `type=t` for English or Latin-based SMS messages. The message value must be URL-encoded before sending.*

```bash
https://api.bulksmsonline.co/smsapi?username=XXXX&password=YYYYY&type=t&to=00000000&source=YourBrand&message=Hello%20customer%2C%20your%20code%20is%201234
```

#### Unicode SMS Example
*Use `type=u` for Unicode messages such as Arabic, Chinese, or other non-Latin languages. The message text must be encoded in UTF-16BE before sending.*

```bash
https://api.bulksmsonline.co/smsapi?username=XXXX&password=YYYYY&type=u&to=00000000&source=YourBrand&message=06450631062D06280627
```

---

## Best Practices

- **Use server-side calls only**: Do not call SMS APIs directly from public browser JavaScript.
- **URL encode messages**: Encode message content and special characters correctly before sending.
- **Store message IDs**: Save returned message IDs to match delivery reports later.
- **Handle rate limits**: Retry safely with backoff when throttling or temporary service errors occur.
- **Validate numbers**: Use MNV and MNP lookup where needed to improve routing and reduce failed sends.
- **Log API responses**: Keep request IDs, message IDs, responses, and callback payloads for support and reconciliation.
