# Netfie Bulk SMS Sender — Web Integration Guide

> **Build your own web-based SMS sender that connects to the Netfie Android App via the Gateway API.**

---

## 📌 What Is This?

The **Netfie Bulk SMS Gateway** is a WordPress plugin + Android app system that lets you send bulk SMS messages **using a real Android phone as the modem**. This documentation shows developers how to build a custom **web front-end** that pushes SMS tasks to the gateway REST API endpoint.

---

## 🏗️ Architecture Overview

```
[Your Web App]
      │
      │  POST /wp-json/netfie/v1/gateway
      ▼
[WordPress Site with Netfie Plugin]
      │   (stores tasks in wp_netfie_queue)
      │
      ▼
[Android App (Netfie)]  ←→  pulls tasks & sends real SMS
      │
      ▼
[Recipient's Phone]
```

1. **Your web app** sends an API request to push SMS tasks.
2. **WordPress** stores those tasks in the queue.
3. **The Android app** polls the queue, picks up tasks, and sends SMS using the phone's SIM card.
4. Status updates are sent back to WordPress automatically.

---

## ⚡ Quick Start (5 Steps)

1. Install WordPress on your server (or use an existing WP site).
2. Install & activate the **Netfie SMS Product** plugin.
3. Go to **WP Admin → Netfie SMS → Settings** and set your **API Key**.
4. Install the **Netfie Android App** on your phone, enter the same API Key and your WordPress site URL.
5. Use the REST API from your web app — see `QUICKSTART.html` and `API_REFERENCE.html`.

---

## 📁 Files in This Folder

| File | Description |
|------|-------------|
| `README.md` | This file — overview & architecture |
| `API_REFERENCE.html` | Full REST API endpoint reference |
| `QUICKSTART.html` | Step-by-step setup guide |
| `integration_example.html` | Live demo HTML page using JavaScript fetch() |
| `GATEWAY_SPEC.txt` | Technical plain-text API specification |
| `web_version_guide.txt` | Full plain-text web integration guide |

---

## 🔗 Gateway Endpoint

```
POST https://yourwebsite.com/wp-json/netfie/v1/gateway
Content-Type: application/x-www-form-urlencoded
```

No WordPress nonce required — auth is handled via the `auth_key` parameter.

---

## 📞 Support

- Plugin: **Netfie SMS Product** (WordPress)
- App: **Netfie Bulk SMS Gateway & Mobile Remote** (Android)
- Website: https://netfie.com

---

*Generated for Netfie SMS Gateway v3.2.0+*
