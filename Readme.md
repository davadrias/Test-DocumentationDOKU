# DOKU PHP SDK Documentation

## Introduction
Welcome to the DOKU PHP SDK! This SDK simplifies access to the DOKU API for your server-side PHP applications, enabling seamless integration with payment and virtual account services.

If your looking for another language, we got: [Node.js](#), [Go](#), [Python](#), [Java](#)

## Table of Contents
1. [Getting Started](#1-getting-started])
2. [Usage](#2-usage)
   - [Virtual Account (DGPC & MGPC)](#a-virtual-account-dgpc)
   - [Virtual Account  (DIPC)](#dipc)
   - [Check Virtual Account Status](#c-check-virtual-account-status)
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

| **Field**       | **Description**                                    | **Required** |
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

| **Field**                | **Description**                                                | **Required** |
|--------------------------|----------------------------------------------------------------|--------------|
| `partnerServiceId`        | The unique identifier for the partner service.                 | ✅           |
| `customerNo`              | The customer's identification number.                          | ✅           |
| `virtualAccountNo`        | The virtual account number associated with the customer.       | ✅           |
| `virtualAccountName`      | The name of the virtual account associated with the customer.  | ✅           |
| `virtualAccountEmail`     | The email address associated with the virtual account.         | ❌           |
| `virtualAccountPhone`     | The phone number associated with the virtual account.         | ❌           |
| `trxId`                   | The unique transaction identifier.                             | ✅           |
| `totalAmount`             |  `Value` <br/>  `Currency`                                                      | ✅ <br/> ✅  |
| `additionalInfo`          | 3 <br/> 4 <br/> 5                                              | 3 <br/> 4 <br/> 5 |
| `virtualAccountTrxType`   | The type of transaction for the virtual account.               | ✅           |
| `expiredDate`             | The expiration date of the virtual account.                    | ❌           |


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
