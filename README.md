<p align="center">
  <a href="https://netfie.com">
    <img src="https://netfie.com/wp-content/uploads/2025/03/Netfie__1_-removebg-preview-450x174.png.webp" alt="Netfie Logo" width="280">
  </a>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/debarjunmaharaj/Netfie-Buik-sms-sender-web/refs/heads/main/icon.png" alt="Netfie Bulk SMS Gateway App Logo" width="110" style="border-radius: 20%;">
</p>

<h1 align="center">Netfie Bulk SMS Gateway — Web Integration & API Documentation</h1>

<p align="center">
  <b>Turn any Android smartphone into a high-speed, cost-effective Bulk SMS Gateway and control it via Web & REST API!</b>
</p>

<p align="center">
  <a href="https://netfie.com"><img src="https://img.shields.io/badge/Official%20Website-Netfie.com-blue?style=for-the-badge" alt="Website"></a>
  <a href="https://wa.me/8801884189495"><img src="https://img.shields.io/badge/WhatsApp-01884189495-25D366?style=for-the-badge&logo=whatsapp&logoColor=white" alt="WhatsApp"></a>
  <a href="mailto:netfieofficial@gmail.com"><img src="https://img.shields.io/badge/Email-netfieofficial%40gmail.com-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Email"></a>
</p>

---

## 🏢 About Netfie (Authorized Web Development Partner)

> **Welcome to Netfie**  
> Welcome to Netfie, the premier web development agency in Bangladesh, where innovation meets excellence. We are a passionate team of expert developers, creative designers, and digital strategists dedicated to building exceptional online experiences. With over 6 years of experience, we specialize in delivering high-quality, innovative web solutions tailored to your business needs, including **premium WordPress themes, powerful plugins, custom Laravel scripts, and robust PHP CMS systems.**

---

## 🌟 What is Netfie Bulk SMS Gateway?

The **Netfie Bulk SMS Gateway** bridges your custom websites, web applications, CRM, or eCommerce stores with real physical Android phones using standard local SIM cards (Grameenphone, Banglalink, Robi, Airtel, Teletalk, etc.). 

Instead of paying expensive third-party SMS aggregator charges, you can use your phone's SMS bundles to deliver OTPs, transactional notifications, promotional bulk SMS, and status updates seamlessly.

```
+--------------------------+       REST API POST       +------------------------------------+
| Your Custom Web App      | ────────────────────────> | WordPress (Netfie Gateway Plugin)  |
| (PHP, JS, Python, cURL)  |                           | Endpoint: /wp-json/netfie/v1/gateway|
+--------------------------+                           +-----------------+------------------+
                                                                         │
                                                                   Store in DB (wp_netfie_queue)
                                                                         │
                                                                         ▼
                                                       +------------------------------------+
                                                       | Netfie Android App Remote Agent     |
                                                       | (Pulls tasks & sends via SIM card) |
                                                       +-----------------+------------------+
                                                                         │
                                                                    GSM Network
                                                                         ▼
                                                       +------------------------------------+
                                                       | Customer Mobile Phone              |
                                                       +------------------------------------+
```

---

## 🚀 Quick Setup Guide

1. **Install Plugin**: Upload and activate the `netfie-sms-product` plugin on your WordPress site.
2. **Configure API Key**: Go to **WordPress Admin → Netfie SMS → Settings**, set your secret `API Key` and configure your preferences.
3. **Connect Android App**: 
   - Open the **Netfie Android App** on your smartphone.
   - Enter your Gateway API Endpoint: `https://yourdomain.com/wp-json/netfie/v1/gateway`
   - Enter your secret `API Key`.
   - Tap **Connect**.
4. **Send SMS via Web API**: Push tasks from any website or backend using standard HTTP POST requests.

---

## 📡 REST API Reference

- **Endpoint URL**: `POST https://yourdomain.com/wp-json/netfie/v1/gateway`
- **Content-Type**: `application/x-www-form-urlencoded` or `application/json`
- **Authentication**: `auth_key` parameter (No WP-Nonce required for external web clients).

### 1. Push SMS Tasks (`action=push`)

Queues single or bulk SMS messages to be sent by your connected Android device.

#### Parameters:
| Field | Type | Required | Description |
|---|---|---|---|
| `auth_key` | string | **Yes** | Your API Key from WP Admin |
| `action` | string | **Yes** | Value must be `push` |
| `numbers` | string | **Yes** | Comma-separated phone numbers (e.g., `01711111111,01822222222`) |
| `message` | string | **Yes** | SMS message text content |
| `sim_slot` | integer | No | `0` for SIM 1, `1` for SIM 2 (Default: `0`) |
| `delay` | integer | No | Delay in seconds between each SMS (Default: `2`) |
| `scheduled_time` | timestamp | No | Unix timestamp (seconds) for scheduled future delivery |

#### Example cURL:
```bash
curl -X POST https://yourdomain.com/wp-json/netfie/v1/gateway \
  -d "auth_key=YOUR_SECRET_API_KEY" \
  -d "action=push" \
  -d "numbers=01711111111,01822222222" \
  -d "message=Hello! Your order has been placed successfully." \
  -d "sim_slot=0" \
  -d "delay=2"
```

#### Success Response:
```json
{
  "success": true,
  "message": "Tasks added",
  "task_ids": [101, 102],
  "skipped": []
}
```

---

### 2. Check & List SMS Tasks (`action=list`)

Retrieve delivery logs, statuses, and queued messages for your web dashboard.

#### Parameters:
| Field | Type | Required | Description |
|---|---|---|---|
| `auth_key` | string | **Yes** | Your API Key |
| `action` | string | **Yes** | Value must be `list` |
| `status` | string | No | Filter by `pending`, `processing`, `sent`, `failed` |
| `limit` | integer | No | Limit records returned (Default: 50, Max: 500) |
| `offset` | integer | No | Pagination offset (Default: 0) |

#### Success Response:
```json
{
  "success": true,
  "total": 1,
  "tasks": [
    {
      "id": 101,
      "number": "01711111111",
      "message": "Hello! Your order has been placed successfully.",
      "sim_slot": 0,
      "status": "sent",
      "created_at": "2026-09-14 10:00:00",
      "sent_at": "2026-09-14 10:00:04"
    }
  ]
}
```

---

## 💻 Integration Code Snippets

### PHP Implementation
```php
<?php
$apiUrl = 'https://yourdomain.com/wp-json/netfie/v1/gateway';
$apiKey = 'YOUR_SECRET_API_KEY';

$payload = [
    'auth_key' => $apiKey,
    'action'   => 'push',
    'numbers'  => '01711111111,01822222222',
    'message'  => 'Greetings from Netfie Web Client!',
    'sim_slot' => 0,
    'delay'    => 2
];

$ch = curl_init($apiUrl);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($payload));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$response = curl_exec($ch);
curl_close($ch);

$result = json_decode($response, true);
print_r($result);
```

### JavaScript / Fetch
```javascript
async function sendBulkSMS(numbers, message) {
  const response = await fetch('https://yourdomain.com/wp-json/netfie/v1/gateway', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      auth_key: 'YOUR_SECRET_API_KEY',
      action: 'push',
      numbers: numbers, // e.g. "01711111111,01822222222"
      message: message,
      sim_slot: 0,
      delay: 2
    })
  });
  const data = await response.json();
  console.log(data);
  return data;
}
```

---

## 👨‍💻 Developer & Company Information

<table border="0">
  <tr>
    <td><b>Agency Name</b></td>
    <td><b>Netfie</b> (Authorized Web Development Partner)</td>
  </tr>
  <tr>
    <td><b>Official Website</b></td>
    <td><a href="https://netfie.com">https://netfie.com</a></td>
  </tr>
  <tr>
    <td><b>Official Email</b></td>
    <td><a href="mailto:netfieofficial@gmail.com">netfieofficial@gmail.com</a></td>
  </tr>
  <tr>
    <td><b>WhatsApp Support</b></td>
    <td><a href="https://wa.me/8801884189495">01884189495</a></td>
  </tr>
  <tr>
    <td><b>Direct Phone</b></td>
    <td><a href="tel:01772326146">01772326146</a></td>
  </tr>
  <tr>
    <td><b>Lead Developer</b></td>
    <td><b>Debarjun Chakraborty</b></td>
  </tr>
  <tr>
    <td><b>Developer Portfolio</b></td>
    <td><a href="https://boost4all.com/@debarjunofficial">https://boost4all.com/@debarjunofficial</a></td>
  </tr>
  <tr>
    <td><b>Developer Facebook</b></td>
    <td><a href="https://www.facebook.com/Debarjunmaharaj/">Debarjun Chakraborty Profile</a></td>
  </tr>
</table>

---

<p align="center">
  <b>© 2026 Netfie. All Rights Reserved. Empowering businesses through smart web & mobile automation.</b>
</p>
