# DOKU PHP SDK Documentation

## Introduction
Welcome to the DOKU PHP SDK! This SDK simplifies access to the DOKU API for your server-side PHP applications, enabling seamless integration with payment and virtual account services.

If your looking for another language, we got: [Node.js](#), [Go](#), [Python](#), [Java](#)

## Table of Contents
1. [Getting Started](#1-getting-started])
2. [Usage](#2-usage)
   - [Virtual Account (DGPC & MGPC)](#a-virtual-account-dgpc)
   - [Virtual Account  (DIPC)](#dipc)
   - [Virtual Account Check Status](#c-check-virtual-account-status)
   - [Binding / Registration](#)
3. [Handling Notifications and Validations](#handling-notifications-and-validations)
4. [Error Handling and Troubleshooting](#error-handling-and-troubleshooting)
5. [Additional Features](#additional-features)
6. [Appendix](#6-appendix)


## 1. Getting Started

### Requirements
- Node.js v18 or higher

### Installation
To install the Doku Snap SDK, use Composer:
```bash
composer require doku/doku-php-library
```

### Configuration
Before using the Doku Snap SDK, you need to initialize it with your credentials:

1. **Client ID** and **Secret Key**: Retrieve these from the Integration menu in your [Doku Dashboard](#)
2. **Private Key**: Generate your Private Key following [Doku Guide](#)

| **Parameter**       | **Description**                                    | **Required** |
|-----------------|----------------------------------------------------|--------------|
| `privateKey`    | The private key for the partner service.           | ✅          |
| `publicKey`     | The public key for the partner service.            | ✅           |
| `clientId`      | The client ID associated with the service.         | ✅           |
| `secretKey`     | The secret key for the partner service.            | ✅           |
| `isProduction`  | Set to true for production environment             | ✅           |
| `issuer`        | Optional issuer for advanced configurations.       | ❌           |
| `authCode`      | Optional authorization code for advanced use.      | ❌           |


```php
use Doku\Snap\Snap;

$privateKey = "YOUR_PRIVATE_KEY";
$publicKey = "YOUR_PUBLIC_KEY";
$clientId = "YOUR_CLIENT_ID";
$secretKey = "YOUR_SECRET_KEY";
$isProduction = false;
$issuer = "YOUR_ISSUER"; 
$authCode = "YOUR_AUTH_CODE"; 

$snap = new Snap($privateKey, $publicKey, $clientId, $issuer, $isProduction, $secretKey, $authCode);
```

## 2. Usage

**Initialization**

Always start by initializing the Snap object.

```php
$snap = new Snap($privateKey, $publicKey, $clientId, $issuer, $isProduction, $secretKey, $authCode);
```

## a. Virtual Account (DGPC & MGPC)
- **Description:** A pre-generated virtual account provided by DOKU.
- **Use Case:** Recommended for one-time transactions.

1. **Create Virtual Account**
   - **Function:** `createVa`
   - **Parameters:** `createVaRequestDto`

<table>
  <thead>
    <tr>
      <th><strong>Parameter</strong></th>
      <th colspan="2"><strong>Description</strong></th>
      <th><strong>Length</strong></th>
      <th><strong>Data Type</strong></th>
      <th><strong>Required</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>partnerServiceId</code></td>
      <td colspan="2">The unique identifier for the partner service.</td>
      <td>1 - 20</td>
      <td>String</td>
      <td>✅</td>
    </tr>
    <tr>
      <td><code>customerNo</code></td>
      <td colspan="2">The customer's identification number.</td>
      <td>1 - 20</td>
      <td>String</td>
      <td>✅</td>
    </tr>
    <tr>
      <td><code>virtualAccountNo</code></td>
      <td colspan="2">The virtual account number associated with the customer.</td>
      <td>1 - 20</td>
      <td>String</td>
      <td>✅</td>
    </tr>
    <tr>
      <td><code>virtualAccountName</code></td>
      <td colspan="2">The name of the virtual account associated with the customer.</td>
      <td>1 - 255</td>
      <td>String</td>
      <td>✅</td>
    </tr>
    <tr>
      <td><code>virtualAccountEmail</code></td>
      <td colspan="2">The email address associated with the virtual account.</td>
      <td>1 - 255</td>
      <td>String</td>
      <td>❌</td>
    </tr>
    <tr>
      <td><code>virtualAccountPhone</code></td>
      <td colspan="2">The phone number associated with the virtual account.</td>
      <td>9 - 30</td>
      <td>String</td>
      <td>❌</td>
    </tr>
    <tr>
      <td><code>trxId</code></td>
      <td colspan="2">Invoice number in Partner system.</td>
      <td>1 - 64</td>
      <td>String</td>
      <td>✅</td>
    </tr>
    <tr>
      <td rowspan="2"><code>totalAmount</code></td>
      <td colspan="2" ><code>value</code>: Transaction Amount (ISO 4217) <br> <small>Example: "11500.00"</small></td>
      <td>1 - 16.2</td>
      <td>String</td>
      <td>✅</td>
    </tr>
    <tr>
      <td colspan="2" ><code>Currency</code>: Currency <br> <small> Example: "IDR"</small> </td>
      <td>1 - 3</td>
      <td>String</td>
      <td>✅</td>
    </tr>
    <tr>
      <td rowspan="4"><code>additionalInfo</code></td>
      <td colspan ="2" ><code>channel</code>: Channel that will be applied for this VA <br> <small>Example: VIRTUAL_ACCOUNT_BANK_CIMB</small></td>
      <td>1 - 20</td>
      <td>String</td>
      <td>✅</td>
    </tr>
    <tr>
     <td rowspan="3"><code>virtualAccountConfig</code></td>
    <td><code>reusableStatus</code>: Reusable Status For Virtual Account Transaction
      <br><small>value TRUE or FALSE</small></td>
      <td>-</td>
      <td>Boolean</td>
      <td>❌</td>
    </tr>
    <tr>
      <td><code>minAmount</code>: Minimum Amount can be use only if virtualAccountTrxType is Open Amount (O). With 2 decimal,format.
      <br><small>Example: "10000.00"</small></td>
      <td>1 - 16.2 </td>
      <td>String</td>
      <td>❌</td>
    </tr>
    <tr>
      <td><code>maxAmount</code>: Maximum Amount can be use only if virtualAccountTrxType is Open Amount (O). With 2 decimal,format. 
      <br><small>Example: "5000000.00"</small>
      </td>
      <td>1-16.2</td>
      <td>String</td>
      <td>❌</td>
    </tr>
    <tr>
      <td ><code>virtualAccountTrxType</code></td>
      <td colspan="2">Transaction type for this transaction. C (Closed Amount), O (Open Amount)</td>
      <td>1</td>
      <td>String</td>
      <td>✅</td>
    </tr>
    <tr>
      <td><code>expiredDate</code></td>
      <td colspan="2">Expiration date for Virtual Account. ISO-8601
        <br><small>Example: "2023-01-01T10:55:00+07:00"</small>
      </td>
      <td>-</td>
      <td>String</td>
      <td>❌</td>
    </tr>
  </tbody>
</table>

   ```php
   use Doku\Snap\Models\VA\Request\CreateVaRequestDto;
   use Doku\Snap\Models\TotalAmount\TotalAmount;
   use Doku\Snap\Models\VA\AdditionalInfo\CreateVaRequestAdditionalInfo;
   use Doku\Snap\Models\VA\VirtualAccountConfig\CreateVaVirtualAccountConfig;

   $createVaRequestDto = new CreateVaRequestDto(
      "8129014",  // partner
      "17223992157",  // customerno
      "812901417223992157",  // customerNo
      "T_" . time(),  // virtualAccountName
      "test.example." . time() . "@test.com",  // virtualAccountEmail
      "621722399214895",  // virtualAccountPhone
      "INV_CIMB_" . time(),  // trxId
      new TotalAmount("12500.00", "IDR"),  // totalAmount
      new CreateVaRequestAdditionalInfo(
            "VIRTUAL_ACCOUNT_BANK_CIMB", new CreateVaVirtualAccountConfig(true)
            ), // additionalInfo
      'C',  // virtualAccountTrxType
      "2024-08-31T09:54:04+07:00"  // expiredDate
   );

   $result = $snap->createVa($createVaRequestDto);
   echo json_encode($result, JSON_PRETTY_PRINT);
   ```

2. **Update Virtual Account**
   - **Function:** `updateVa`
   - **Parameters** `updateVaRequestDto`

| **Field**           | **Description**                                                | **Required** |
|---------------------|----------------------------------------------------------------|--------------|
| `partnerServiceId`   | The unique identifier for the partner service.                | Yes          |
| `customerNo`         | The customer's identification number.                         | Yes          |

```javascript
const updateVaRequestDto = /* request setup */;
const response = await snap.updateVa(updateVaRequestDto);
```

3. **Delete Virtual Account**
   - **Function:** `deletePaymentCode`
   - **Parameters** `deleteVaRequestDto`


### b. Virtual Account (DIPC)
- **Description:** Custom virtual account codes created by the merchant.
- **Use Case:** Useful when merchants require custom payment identifiers.

### c. Check Virtual Account Status
- **Description:** Custom virtual account codes created by the merchant.
- **Use Case:** Useful when merchants require custom payment identifiers.



## 3. Handling Notifications and Validations

After a customer completes payment, you’ll receive a notification. Here’s how to process notifications and validate them:

1. **Generate Token for Notification**
   - **Function:** `validateSignatureAndGenerateToken`

```javascript
const tokenResponse = snap.validateSignatureAndGenerateToken(request, endPointUrl);
```

2. **Handle Notification Response**
   - **Function:** `generateNotificationResponse`

```javascript
const notificationResponse = snap.generateNotificationResponse(isTokenValid, requestBody);
   ```

## 4. Error Handling and Troubleshooting

This section provides common errors and solutions:

| Error Code | Description                           | Solution                                     |
|------------|---------------------------------------|----------------------------------------------|
| `4010000`  | Unauthorized                          | Check if Client ID and Secret Key are valid. |
| `4012400`  | Virtual Account Not Found             | Verify the virtual account number provided.  |
| `2002400`  | Successful                            | Transaction completed successfully.          |

## 5. Additional Features

### v1 to SNAP Converter
For users upgrading from v1 APIs, the SDK provides conversion utilities.

1. **Convert Inquiry from v1 to SNAP**
   ```javascript
   const formData = snap.directInquiryRequestMapping(request.headers, request.body);
   ```

2. **Convert Response from v1 to SNAP**
   ```javascript
   const xmlToJson = snap.directInquiryResponseMapping(xmlResponse);
   ```

## 6. Appendix

### Glossary
- **VA**: Virtual Account, a temporary payment identifier.
- **MGPC**: Merchant-Generated Payment Code.

### FAQ
- **How do I set up a virtual account?**
  See [Payment Flow and Virtual Account Setup](#payment-flow-and-virtual-account-setup).
