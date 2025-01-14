---
sidebar_position: 2
sidebar_label: Verification
description: The Verification API is used to verify the form data.
---

# Verification

## `verify`

**Method**: POST<br />
**Endpoint**: /api/v1/verification/verify<br />
**Content-Type**: application/json (payload as JSON string in the request body)

Verifies the form data and tells the website's backend if a submission was correct or manipulated.

### Authentication

To secure the API endpoint, authentication is required. The `Authorization` header must be sent with the request. You must set the project's public key in the header as the username. An HMAC SHA256 hash of the API endpoint URL combined with the request data, serialized as JSON, must be set as the password. The private key will be used as the key for the HMAC SHA256 hash.

```http request
Authorization: [base64 of <publicKey>:<hmacHash>]
```

#### Example

```php
$publicKey = 'XStQNakEiJk1oMIXJ6_Rxmd3j5gNcQae34n1G3aR6FU';
$privateKey = 'stH6Ugo4FcbQLp6_KPlOYltFMHfY59rxCUQRk3_AxYQ';
$apiEndpoint = '/api/v1/verification/verify';
$formData = ['first-name' => '0fde7e04a97f64098b5285c6e33502ddd918a04a7fc8c7012a13caae19b26c3b'];

$hmacHash = hash_hmac('sha256', $apiEndpoint . json_encode($formData), $privateKey);
$authHeader = base64_encode($publicKey . ':' . $hmacHash);
```

```http request
Authorization: WFN0UU5ha0VpSmsxb01JWEo2X1J4bWQzajVnTmNRYWUzNG4xRzNhUjZGVTpRcWZCeHNtT2ZJTXcwLXVWTm5SVmREbE1VWmRMcFRHMXhvMHl5aWZ5THJJOjNiZGQzODVjYWE1M2UzZGE3NmE4ZGNiZmNhYTBkOWY0ZTA0ZDhjMTg5ZmFiMDNiYTQxMzgzZGVlYTIzNmIyZDM=
```

### Request

#### Example
```json
{
  "submitToken":"_wc0MPl5EQuwuJeTMq8uoF7WFpFdoZZf35ctawmasmc",
  "validationSignature":"122fe5123d3efb8167000b1adf54864991208f9ab9192b66d178cfc1886ed12d",
  "formSignature":"b1e232b17f9cb11ea9402cbdf67325f3ecc494bfa2277e3246f1f3a51696b668",
  "formData":{
    "email-address":"90adf74020cede3f838394bfc64d2981f7a60f06bd91dd55fcdf299970a3b1b9",
    "first-name":"0fde7e04a97f64098b5285c6e33502ddd918a04a7fc8c7012a13caae19b26c3b",
    "last-name":"65b63117b1a4dfe468d927e32d8ea302d9d10c04c9b9b7dfac9c7770deacc0cc",
    "message":"2c1733ff5e4e9a7f206c4fff391021acc6b1783785dbd70be9fcb8d008a0d9e5",
    "website":"e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  }
}
```

#### Arguments

| Name                  | Type   | Required | Description                                                                     |
|-----------------------|--------|----------|---------------------------------------------------------------------------------|
| `submitToken`         | String | Required | The submit token was requested by the JavaScript script in the frontend.        |
| `validationSignature` | String | Required | The HMAC SHA256 hash of the validation token.                                   |
| `formSignature`       | String | Required | The HMAC SHA256 hash of the form data (serialized as JSON string).              |
| `formData`            | Object | Required | An object with all form fields and the SHA256 hash of the data for every field. |

### Response

#### Example
```json
{
  "valid":true,
  "verificationSignature":"994937080e6f4fdfbe0aa8e0581348cbabc1d3b84365e8a8ba0a00fa2716e470",
  "verifiedFields":{
    "first-name":"valid",
    "last-name":"valid",
    "email-address":"valid",
    "website":"valid",
    "message":"valid"
  },
  "issues":[

  ]
}
```

#### Properties

If mosparo completed the request successfully, the following properties would be present in the answer:

| Name                    | Type     | Description                                                                                                                                                                                                    |
|-------------------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `valid`                 | Boolean  | This Is true if the request was valid and can be processed by the backend. If this is false, the submission is not acceptable.                                                                                 |
| `verificationSignature` | String   | The signature generated by mosparo. This needs to be checked with the signature created by the backend itself to prevent manipulation (see [Evaluate the answer](../integration/custom/#evaluate-the-answer)). |
| `verifiedFields`        | Object   | An object with all verified fields and with the status for every field (see [Values for `verifiedFields`](../integration/custom/#values-for-verifiedfields)).                                                  |
| `issues`                | Array    | An array with possible issues as a string.                                                                                                                                                                     |

If an error occurred, only the following properties would be present in the answer:

| Name           | Type    | Description                                    |
|----------------|---------|------------------------------------------------|
| `error`        | Boolean | If true, an error occurred.                    |
| `errorMessage` | String  | The description of the error which occurred.   |
