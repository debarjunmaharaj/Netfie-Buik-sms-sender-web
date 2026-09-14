<div align="center">

<a href="https://netfie.com" target="_blank">
  <img src="https://netfie.com/wp-content/uploads/2025/03/Netfie__1_-removebg-preview-450x174.png.webp" alt="Netfie Official Agency Logo" width="300">
</a>

<br><br>

<img src="https://raw.githubusercontent.com/debarjunmaharaj/Netfie-Buik-sms-sender-web/refs/heads/main/icon.png" alt="Netfie App Logo" width="120" style="border-radius: 24px; box-shadow: 0 8px 24px rgba(0,0,0,0.15);">

# 📱 Netfie Bulk SMS Gateway & Mobile Remote
### *Turn Any Android Device into an Enterprise-Grade, Self-Hosted SMS Gateway*

[![Netfie Website](https://img.shields.io/badge/Netfie-Official%20Site-0070f3?style=for-the-badge&logo=google-chrome&logoColor=white)](https://netfie.com)
[![WhatsApp Support](https://img.shields.io/badge/WhatsApp-01884189495-25D366?style=for-the-badge&logo=whatsapp&logoColor=white)](https://wa.me/8801884189495)
[![Direct Call](https://img.shields.io/badge/Call%20Us-01772326146-orange?style=for-the-badge&logo=phone&logoColor=white)](tel:01772326146)
[![Developer Portfolio](https://img.shields.io/badge/Developer-Debarjun%20Chakraborty-8A2BE2?style=for-the-badge&logo=visual-studio-code&logoColor=white)](https://boost4all.com/@debarjunofficial)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

<p align="center">
  <b>Zero Aggregator Fees • Dual SIM Support • Any Backend Stack • Complete Hardware Independence</b>
</p>

---

</div>

## 📑 Table of Contents

- [🏢 About Netfie](#-about-netfie)
- [💡 Architectural Overview](#-architectural-overview)
- [🔄 Protocol Flow & JSON Payloads](#-protocol-flow--json-payloads)
- [📚 Platform-Specific Integration Guides](#-platform-specific-integration-guides)
- [🗄️ Database Schema & Standalone Backend](#-database-schema--standalone-backend)
- [🎯 Real-World Industry Use Cases](#-real-world-industry-use-cases)
- [👨‍💻 Developer & Company Details](#-developer--company-details)

---

## 🏢 About Netfie

> **Welcome to Netfie — Authorized Web Development Partner**  
> We are the premier web development agency in Bangladesh, where innovation meets digital excellence. Our team of seasoned software engineers, UI/UX designers, and systems architects specializes in developing world-class web and mobile systems tailored to high-growth businesses.  
> With **over 6 years of expertise**, our flagship capabilities include:
> - **Enterprise WordPress & WooCommerce Architecture** (Themes, Core Plugins, Headless Systems)
> - **Custom Laravel & Symfony Web Applications & SaaS**
> - **High-Performance Node.js & Python Automation Pipelines**
> - **Custom Telephony & SMS Gateway Infrastructure**

---

## 💡 Architectural Overview

Traditional SMS aggregators charge heavy rates per message and impose strict registration hurdles. **Netfie Bulk SMS Gateway** removes these limits:

1. **You control the Backend**: Build your backend anywhere (Laravel, PHP, Node.js, Python, Go, WordPress, etc.).
2. **Netfie provides the Mobile Remote App**: Install our Android/Flutter app on your phone, point it to your server endpoint, and enter your secret API key.
3. **Automated Queueing**: When your website or application creates an SMS task, the mobile app polls your endpoint, pulls the message, and dispatches it over local GSM networks using your phone's SIM bundle.

```
┌─────────────────────────────────────────────────────────────┐
│                 YOUR CUSTOM SERVER / BACKEND                │
│    (Laravel, Core PHP, Node.js, Python, Go, Sheets, etc.)   │
│                                                             │
│  • Exposes REST Endpoint: https://domain.com/sms-gateway    │
│  • Enqueues messages to database: status = 'pending'        │
└──────────────────────────────▲──────────────────────────────┘
                               │
            1. POST (pull)     │   3. POST (update_status)
            Fetch queued tasks │   Report 'sent' / 'failed'
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                NETFIE MOBILE REMOTE (FLUTTER APP)           │
│                                                             │
│  • User configures Server Endpoint URL & Auth Key           │
│  • Dispatches SMS via Android Telephony (SIM 1 or SIM 2)    │
│  • Configurable delays (2-5s) to avoid carrier rate limits  │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               │ Cellular Carrier (GSM)
                               ▼
                 [End Customer Mobile Handset]
```

---

## 🔄 Protocol Flow & JSON Payloads

Your custom server endpoint must accept **HTTP POST** requests and support standard JSON or form payloads.

### 1. `pull` — App Pulls Pending SMS
Sent periodically by the Netfie Flutter App to discover pending messages.

- **Request Body (from App to your Server):**
```json
{
  "auth_key": "YOUR_SECRET_API_KEY",
  "action": "pull",
  "device_id": "optional-android-device-identifier"
}
```

- **Expected Response (from your Server to App):**
```json
{
  "success": true,
  "tasks": [
    {
      "id": 101,
      "number": "01711111111",
      "message": "Your OTP code is 938210. Valid for 5 minutes.",
      "sim_slot": 0,
      "delay": 2
    }
  ]
}
```

---

### 2. `update_status` — App Reports Delivery Status
Sent by the phone immediately after transmitting the message.

- **Request Body (from App to your Server):**
```json
{
  "auth_key": "YOUR_SECRET_API_KEY",
  "action": "update_status",
  "task_id": 101,
  "status": "sent"
}
```
*(Status values: `sent`, `failed`)*

- **Expected Response:**
```json
{
  "success": true,
  "message": "Status updated successfully"
}
```

---

### 3. `push` — Enqueue New SMS (Your Web App / CRM)
Used by your web applications, forms, and triggers to insert tasks into your queue.

- **Request Body:**
```json
{
  "auth_key": "YOUR_SECRET_API_KEY",
  "action": "push",
  "numbers": "01711111111,01822222222",
  "message": "Flash Sale! Enjoy 25% off today at Netfie.",
  "sim_slot": 0,
  "delay": 2
}
```

- **Expected Response:**
```json
{
  "success": true,
  "message": "Tasks added",
  "task_ids": [101, 102],
  "skipped": []
}
```

---

## 📚 Platform-Specific Integration Guides

We have created dedicated, fully-documented integration manuals and code files for every major programming stack:

| Framework / Language | Integration File | HTML Guide | TXT Doc |
|---|---|---|---|
| **Laravel / Symfony** | [LARAVEL_SYMFONY.md](integrations/LARAVEL_SYMFONY.md) | [LARAVEL.html](integrations/LARAVEL.html) | [LARAVEL.txt](integrations/LARAVEL.txt) |
| **Pure / Core PHP** | [PURE_PHP.md](integrations/PURE_PHP.md) | [PHP.html](integrations/PHP.html) | [PHP.txt](integrations/PHP.txt) |
| **Node.js / Express / NestJS** | [NODEJS.md](integrations/NODEJS.md) | [NODEJS.html](integrations/NODEJS.html) | [NODEJS.txt](integrations/NODEJS.txt) |
| **Python / Django / FastAPI** | [PYTHON.md](integrations/PYTHON.md) | [PYTHON.html](integrations/PYTHON.html) | [PYTHON.txt](integrations/PYTHON.txt) |
| **Go / Golang** | [GOLANG.md](integrations/GOLANG.md) | [GOLANG.html](integrations/GOLANG.html) | [GOLANG.txt](integrations/GOLANG.txt) |
| **Flutter / React Native** | [MOBILE_CLIENT.md](integrations/MOBILE_CLIENT.md) | [MOBILE.html](integrations/MOBILE.html) | [MOBILE.txt](integrations/MOBILE.txt) |
| **WordPress / WooCommerce** | [WORDPRESS_WOOCOMMERCE.md](integrations/WORDPRESS_WOOCOMMERCE.md) | [WORDPRESS.html](integrations/WORDPRESS.html) | [WORDPRESS.txt](integrations/WORDPRESS.txt) |
| **Google Sheets (App Script)** | [GOOGLE_SHEETS.md](integrations/GOOGLE_SHEETS.md) | [GOOGLE_SHEETS.html](integrations/GOOGLE_SHEETS.html) | [GOOGLE_SHEETS.txt](integrations/GOOGLE_SHEETS.txt) |

---

## 🗄️ Database Schema & Standalone Backend

### Ready-to-Run MySQL Table:
```sql
CREATE TABLE `sms_queue` (
  `id` INT AUTO_INCREMENT PRIMARY KEY,
  `number` VARCHAR(30) NOT NULL,
  `message` TEXT NOT NULL,
  `sim_slot` TINYINT DEFAULT 0,
  `delay` INT DEFAULT 2,
  `status` ENUM('pending', 'processing', 'sent', 'failed') DEFAULT 'pending',
  `scheduled_at` DATETIME NULL,
  `sent_at` DATETIME NULL,
  `created_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  INDEX (`status`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

---

## 🎯 Real-World Industry Use Cases

| Industry | Implementation |
|---|---|
| 🛒 **E-Commerce & Retail** | Order confirmation SMS, OTP verification on Cash on Delivery, abandoned cart reminders, delivery courier status. |
| 🔐 **Authentication & Security** | Fast login OTPs, 2FA codes, password reset verification tokens. |
| 🏥 **Hospitals & Clinics** | Doctor appointment schedules, diagnostic test report notifications. |
| 🏫 **Schools & Universities** | Automated daily absence notifications to parents, marksheet/result broadcasts, fee reminders. |
| 🌐 **ISP & Cable Networks** | Monthly bill reminders, invoice links, payment confirmation, automatic disconnection warnings. |
| 📦 **Couriers & Logistics** | Parcel out-for-delivery alerts, rider tracking phone numbers, delivery handover OTPs. |
| 💻 **Server & DevOps Alerts** | Immediate SMS notification when websites or servers go offline. |

---

## 👨‍💻 Developer & Company Details

<table border="0">
  <tr>
    <td width="200"><b>Agency Name</b></td>
    <td><b>Netfie</b> (Authorized Web Development Partner)</td>
  </tr>
  <tr>
    <td><b>Official Website</b></td>
    <td><a href="https://netfie.com" target="_blank">https://netfie.com</a></td>
  </tr>
  <tr>
    <td><b>Email Inquiries</b></td>
    <td><a href="mailto:netfieofficial@gmail.com">netfieofficial@gmail.com</a></td>
  </tr>
  <tr>
    <td><b>WhatsApp Support</b></td>
    <td><a href="https://wa.me/8801884189495">01884189495</a></td>
  </tr>
  <tr>
    <td><b>Direct Phone Call</b></td>
    <td><a href="tel:01772326146">01772326146</a></td>
  </tr>
  <tr>
    <td><b>Lead Developer</b></td>
    <td><b>Debarjun Chakraborty</b></td>
  </tr>
  <tr>
    <td><b>Developer Portfolio</b></td>
    <td><a href="https://boost4all.com/@debarjunofficial" target="_blank">https://boost4all.com/@debarjunofficial</a></td>
  </tr>
  <tr>
    <td><b>Developer Facebook</b></td>
    <td><a href="https://www.facebook.com/Debarjunmaharaj/" target="_blank">Debarjun Chakraborty Profile</a></td>
  </tr>
</table>

---

<div align="center">
  <sub>© 2026 Netfie. All Rights Reserved. Built with pride by Debarjun Chakraborty & the Netfie Engineering Team.</sub>
</div>
