# Webhooks

Here's a comprehensive documentation guide for integrating and managing webhooks in Fluxvend. This guide will walk developers through the setup, security, and usage of webhooks for linking POS card transactions or bank transactions to a sales order in Fluxvend.
<img src="developer-intergration.png" alt="Developer Page"/>
### Overview

Fluxvend's webhooks allow you to seamlessly link POS card transactions or bank transactions to a sales order within Fluxvend. This dynamic and secure webhook system ensures that your transactions are accurately mapped to your inventory sales orders, offering both flexibility and robust security features.

### Webhook Security

Fluxvend's webhook system employs multiple layers of security:

1. **IP Whitelisting**: Only requests from IP addresses that have been explicitly whitelisted will be accepted.
2. **API Signature**: For Fluxvend’s core webhook, each request must include a signature generated using HmacSHA256 and your API secret key. This signature is sent in the `Signature` header.

#### Generating the API Signature

To authenticate your webhook request, you'll need to create an HmacSHA256 hash of the request payload using your API secret key and then encode the result in Base64. This signature is included in the `Signature` header of the request.

Here’s how to generate the signature:

1. **Create an HmacSHA256 hash** using your API secret key + Api Secret.
2. **Compute the Base64 hash** of the HmacSHA256 result.
3. **Include the Base64-encoded signature** in the `Signature` header of your webhook request.

#### Request Payload

The request payload is the data you send to the webhook endpoint. The endpoint accepts json data, which can include transaction details, order information, or any other relevant data you want to link to a sales order in Fluxvend.

> **Note**: See the example json payload below:

```json
{
    "tenantId": "tenant123",
    "transactionRef": "TXN123456789",
    "paymentRef": "PAY123456789",
    "paymentMethod": "CARD",
    "amount": 1000.00,
    "customer": {
        "id": "cust123",
        "name": "Jane Doe",
        "email": "jane.doe@example.com",
        "phone": "+1234567890"
    },
    "transactionDate": "2024-08-13T15:30:00Z",
    "accountNumber": "1234567890",
    "bankCode": "032",
    "bankName": "First Bank",
    "accountName": "John Doe"
}

```

#### Example 

Here’s an example of how you might generate the signature:

<tabs>
<tab title="Java">

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.util.Base64;

public class HMACSignatureGenerator {

    public static void main(String[] args) {
        try {
            String apiKey = "your_api_key";
            String secretKey = "your_secret_key";
            String requestPayload = "{ /* Your request payload */ }";

            String dataToSign = apiKey + ":" + secretKey;

            // Create HMAC SHA256 signature
            Mac sha256Hmac = Mac.getInstance("HmacSHA256");
            SecretKeySpec keySpec = new SecretKeySpec(secretKey.getBytes(), "HmacSHA256");
            sha256Hmac.init(keySpec);
            byte[] hmacData = sha256Hmac.doFinal(requestPayload.getBytes());
            String signature = Base64.getEncoder().encodeToString(hmacData);

            // Print or use the signature as needed
            System.out.println("Signature: " + signature);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
</tab>


<tab title="Javascript">

```Javascript
const crypto = require('crypto');

const apiKey = 'your_api_key';
const secretKey = 'your_api_secret_key';
const requestBody = JSON.stringify({ /* Your request payload */ });

// Generate HMAC SHA256 signature
const hmac = crypto.createHmac('sha256', apiKey + ':' + secretKey);
hmac.update(requestBody);
const signature = hmac.digest('base64');

// Include signature in the request header
const headers = {
    'Signature': signature,
    'Content-Type': 'application/json',
};

// Example request (using axios)
axios.post('https://your-webhook-url.com', requestBody, { headers })
    .then(response => {
        console.log('Webhook sent successfully:', response.data);
    })
    .catch(error => {
        console.error('Error sending webhook:', error.response.data);
    });
```
</tab>

<tab title="C#">

```C#
using System;
using System.Security.Cryptography;
using System.Text;

public class HMACSignatureGenerator
{
    public static void Main()
    {
        string apiKey = "your_api_key";
        string secretKey = "your_secret_key";
        string requestPayload = "{ /* Your request payload */ }";

        string dataToSign = apiKey + ":" + secretKey;

        // Create HMAC SHA256 signature
        using (var hmacsha256 = new HMACSHA256(Encoding.UTF8.GetBytes(secretKey)))
        {
            byte[] hashBytes = hmacsha256.ComputeHash(Encoding.UTF8.GetBytes(requestPayload));
            string signature = Convert.ToBase64String(hashBytes);

            // Print or use the signature as needed
            Console.WriteLine("Signature: " + signature);
        }
    }
}

```
</tab>


<tab title="PHP">

```PHP
<?php
$apiKey = 'your_api_key';
$secretKey = 'your_secret_key';
$requestPayload = '{ /* Your request payload */ }';

$dataToSign = $apiKey . ':' . $secretKey;

// Create HMAC SHA256 signature
$signature = base64_encode(hash_hmac('sha256', $requestPayload, $secretKey, true));

// Print or use the signature as needed
echo "Signature: " . $signature;
?>
```
</tab>




</tabs>

### Managing Webhooks

#### Enabling and Configuring Webhooks

To start using webhooks in Fluxvend, follow these steps:

1. **Login to the Admin Portal**: Navigate to the Fluxvend Admin Portal.
2. **Go to the Developer Page**:
    - Click on your company logo at the top right of the page.
    - Select your account name from the dropdown.
    - Navigate to the **Developer** tab.
3. **Access the Integrations Section**:
    - Within the Developer tab, go to the **Integrations** section.
    - Here, you will see options to enable different webhooks.
    - Webhooks are disabled by default. Click on the **Add** button to enable them.
4. **Manage Webhooks**:
    - After enabling a webhook, you will be redirected to the management page.
    - On this page, you can **copy your webhook url**, **whitelist IP addresses**, **manage your API credentials**, and **disable** webhooks when they are no longer needed.

   > **Note**: Disabling a webhook deletes it entirely from our system, including any associated IP whitelisting. If you decide to re-enable a webhook, you'll need to re-whitelist IPs and reconfigure any necessary settings.

<img src="monnify.png" alt="Developer Page"/>
#### Supported Webhooks

1. **Fluxvend Core Webhook**:
    - **Purpose**: Link external payment transactions from any source to a sales order in Fluxvend.
    - **Security**: Requires both IP whitelisting and API signature in the request header.

2. **Monnify Webhook**:
    - **Purpose**: Integrate Monnify POS card terminal transactions, bank transfers, and other payment methods with Fluxvend's sales orders.
    - **Security**: Requires IP whitelisting (API signature not required).

3. **Opay Webhook**:
    - **Purpose**: Integrate Opay POS card terminal transactions, bank transfers, and other payment methods with Fluxvend's sales orders.
    - **Security**: Requires IP whitelisting (API signature not required).

<note>
    Fluxvend has pre-integrated support for Monnify and Opay, the most widely used POS card terminal providers. These webhooks allow you to automatically link transactions from these providers to your sales orders in Fluxvend.
</note>




### Webhook Management Capabilities

Fluxvend provides extensive management capabilities for your webhooks:

- **Regenerate Secret**: You can regenerate the secret key for your API credentials.
- **Delete Scoped Credentials**: Remove any scoped credentials that are no longer in use.
- **Add Scoped Credentials**: Add up to 5 scoped credentials for specific use cases.
- **IP Whitelisting**: Secure your webhook by specifying which IP addresses are allowed to send requests.
- **Disable/Enable Webhooks**: Temporarily disable webhooks when not in use. Remember, disabling will delete them, so reconfiguration is necessary upon re-enabling.

---

This documentation guide will help developers integrate, secure, and manage their webhooks effectively within Fluxvend. If you need more detailed examples or further customization, feel free to reach out!
