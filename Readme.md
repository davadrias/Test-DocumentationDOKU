# DOKU Node.js SDK Documentation

## Introduction
Welcome to the DOKU Node.js SDK! This SDK simplifies access to the DOKU API for your server-side JavaScript applications, enabling seamless integration with payment and virtual account services.

If your looking for another language, we got: [PHP](#), [Go](#), [Python](#), [Java](#)

## Table of Contents
1. [Getting Started](#1-getting-started])
2. [Usage](#2-usage)
   - [Virtual Account (DGPC)](#a-virtual-account-dgpc)
   - [Virtual Account (MGPC)](#)
   - [Virtual Account  (DIPC)](#dipc)
3. [Handling Notifications and Validations](#handling-notifications-and-validations)
4. [Error Handling and Troubleshooting](#error-handling-and-troubleshooting)
5. [Additional Features](#additional-features)
6. [Appendix](#6-appendix)

## 1. Getting Started

### Requirements
- Node.js v18 or higher

### Installation
Install the SDK using npm:
```bash
npm install doku
```

### Configuration
Before using the Doku Snap SDK, you need to initialize it with your credentials:

1. **Client ID** and **Secret Key**: Retrieve these from the Integration menu in your [Doku Dashboard](#)
2. **Private Key**: Generate your Private Key following [Doku Guide](#)


```javascript
const doku = require('doku');

const snap = new doku.Snap({
   isProduction: false,
   clientId: 'your_client_id_here'
   secretKey: 'your_secret_key_here'
   privateKey: 'your_private_key_here',
});

```

## 2. Usage

**Initialization**

Always start by initializing the Snap object:

```javascript
const snap = new doku.Snap({})
```

### a. Virtual Account (DGPC)
- **Description:** A pre-generated virtual account provided by DOKU.
- **Use Case:** Recommended for one-time transactions.

1. **Create Virtual Account**
   - **Function:** `createVa`
   - **Parameters:** `createVaRequestDto`

| **Field**           | **Description**                                                | **Required** |
|---------------------|----------------------------------------------------------------|--------------|
| `partnerServiceId`   | The unique identifier for the partner service.                 | Yes          |
| `customerNo`         | The customer's identification number.                          | Yes          |
| `virtualAccountNo`   | The virtual account number associated with the customer.       | Yes          |

   ```javascript
   // Required or import goes here

   const createVaRequestDto = new doku.CreateVARequestDto({
     partnerServiceId: '999999',
     customerNo: '0000000',
     virtualAccountNo: '9999990000000000',
     // additional parameters
   });

   const response = await snap.createVa(createVaRequestDto);
   console.log("Payment code:", response.paymentCode);
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

### b. Virtual Account (MGPC)
- **Description:** Custom virtual account codes created by the merchant.
- **Use Case:** Useful when merchants require custom payment identifiers.

### c. Virtual Account (DIPC)
- **Description:** Merchants validate payments directly without contacting DOKU.
- **Use Case:** Useful for high-security transactions.

### d. Direct Debit
- **Description:** Direct Debit
- **Use Case:** Direct Debit

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
