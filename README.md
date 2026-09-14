<p align="center">
  <a href="https://netfie.com">
    <img src="https://netfie.com/wp-content/uploads/2025/03/Netfie__1_-removebg-preview-450x174.png.webp" alt="Netfie Logo" width="280">
  </a>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/debarjunmaharaj/Netfie-Buik-sms-sender-web/refs/heads/main/icon.png" alt="Netfie Bulk SMS Gateway App Logo" width="110" style="border-radius: 20%;">
</p>

<h1 align="center">Netfie Bulk SMS Gateway — Self-Hosted Backend & Web Integration Guide</h1>

<p align="center">
  <b>Build your own custom backend API & Web dashboard in any language (Laravel, PHP, Node.js, Python, etc.) and turn any Android phone into an automated SMS Gateway using the Netfie Mobile Remote App!</b>
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

## 💡 How It Works (The Self-Hosted Concept)

You **do not need WordPress** if you don't want to use it! You can host your own custom endpoint in **any language or framework**:
- **Laravel / Symfony / CodeIgniter**
- **Pure Core PHP / Custom CMS**
- **Node.js (Express / Fastify / NestJS)**
- **Python (Django / FastAPI / Flask)**
- **Go / Ruby on Rails / ASP.NET**
- **WordPress** (using our pre-built plugin or custom REST route)

### 📲 What Netfie Provides:
We provide the **Netfie Android / Flutter App**. On the app settings screen, you simply enter:
1. **Your Custom Server URL / Endpoint** (e.g. `https://your-domain.com/api/sms/gateway`)
2. **Your Secret API Key**

The app automatically communicates with your custom backend, pulls pending SMS tasks from your database, sends them using your physical SIM cards (SIM 1 or SIM 2), and reports status back to your server.

```
+-----------------------------------------------------------------------------------+
|                        YOUR CUSTOM SERVER / BACKEND                               |
|   (Your Web Dashboard, Laravel, Node.js, PHP, Python, CRM, E-Commerce, etc.)      |
|                                                                                   |
|   1. Creates SMS tasks & stores in database (queue table)                         |
|   2. Hosts Endpoint: https://your-server.com/your-endpoint                        |
+-----------------------------------------▲-----------------------------------------+
                                          │
                        HTTP POST Requests│(poll every few seconds)
                                          │
+-----------------------------------------▼-----------------------------------------+
|                         NETFIE ANDROID / FLUTTER APP                              |
|                                                                                   |
|   - Configured with: Server URL & Secret API Key                                  |
|   - Sends action: "pull" -> fetches pending tasks                                 |
|   - Dispatches SMS via Phone SIM Card (SIM 1 or SIM 2)                            |
|   - Sends action: "update_status" -> notifies server of success / failure         |
+-----------------------------------------▲-----------------------------------------+
                                          │ GSM Network
                                          ▼
                               [Customer Mobile Phones]
```

---

## 🛠️ Implementing Your Custom Server Endpoint

Your server endpoint must accept **HTTP POST** requests and handle the following actions based on the `action` parameter.

---

### Action 1: `pull` (App fetching pending SMS tasks)
The Netfie Flutter app continuously calls this action to check if there are any SMS tasks waiting to be sent.

#### App Request to Server:
- **Method**: `POST`
- **Headers**: `Content-Type: application/x-www-form-urlencoded` or `application/json`
- **Request Body**:
```json
{
  "auth_key": "YOUR_CUSTOM_SECRET_KEY",
  "action": "pull",
  "device_id": "optional-device-hardware-id"
}
```

#### Expected Server Response:
Return a JSON object containing a `tasks` array. If no tasks are pending, return an empty array `[]`.

```json
{
  "success": true,
  "tasks": [
    {
      "id": 101,
      "number": "01711111111",
      "message": "Your verification code is 482910",
      "sim_slot": 0,
      "delay": 2
    },
    {
      "id": 102,
      "number": "01822222222",
      "message": "Thank you for your order #4521!",
      "sim_slot": 1,
      "delay": 3
    }
  ]
}
```

> **Field Explanations:**
> - `id` *(integer/string)*: Unique identifier of the task in your database.
> - `number` *(string)*: Target phone number with or without country code.
> - `message` *(string)*: Text of the SMS message.
> - `sim_slot` *(integer)*: `0` for SIM 1, `1` for SIM 2.
> - `delay` *(integer)*: Delay in seconds to wait before sending this SMS (anti-ban pacing).

---

### Action 2: `update_status` (App reports SMS status)
After the phone dispatches the SMS via the Android telephony manager, it informs your server of the result.

#### App Request to Server:
- **Method**: `POST`
- **Request Body**:
```json
{
  "auth_key": "YOUR_CUSTOM_SECRET_KEY",
  "action": "update_status",
  "task_id": 101,
  "status": "sent"
}
```
*(Status values: `sent`, `failed`, or `processing`)*

#### Expected Server Response:
```json
{
  "success": true,
  "message": "Status updated successfully"
}
```

---

### Action 3: `push` (Web Client or Backend adding new SMS tasks)
You can use this action internally on your endpoint to queue SMS messages from your web dashboard, forms, or external apps.

#### Request:
```json
{
  "auth_key": "YOUR_CUSTOM_SECRET_KEY",
  "action": "push",
  "numbers": "01711111111,01822222222,01933333333",
  "message": "Special 20% discount offer! Use code NETFIE20",
  "sim_slot": 0,
  "delay": 2,
  "scheduled_time": 1726300000
}
```

#### Expected Response:
```json
{
  "success": true,
  "message": "Tasks queued successfully",
  "task_ids": [101, 102, 103],
  "skipped": []
}
```

---

### Action 4: `list` (Fetch task status / logs for your Web UI)
Optional helper action to view logs on your web UI.

#### Request:
```json
{
  "auth_key": "YOUR_CUSTOM_SECRET_KEY",
  "action": "list",
  "status": "sent",
  "limit": 50,
  "offset": 0
}
```

#### Expected Response:
```json
{
  "success": true,
  "total": 1,
  "tasks": [
    {
      "id": 101,
      "number": "01711111111",
      "message": "Your verification code is 482910",
      "sim_slot": 0,
      "status": "sent",
      "created_at": "2026-09-14 10:00:00",
      "sent_at": "2026-09-14 10:00:04"
    }
  ]
}
```

---

## 🗄️ Recommended Database Schema (SQL)

If you are building your own backend from scratch, here is a clean table structure:

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
  `created_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 💻 Full Custom Backend Example (Pure PHP in 1 File)

Save this as `gateway.php` anywhere on your server (e.g., `https://your-domain.com/gateway.php`):

```php
<?php
header('Content-Type: application/json');

$SECRET_KEY = "my_super_secret_api_key_12345";

// Retrieve input data (Supports both JSON body and POST form-data)
$rawInput = file_get_contents('php://input');
$data = json_decode($rawInput, true) ?: $_POST;

$authKey = $data['auth_key'] ?? '';
$action  = $data['action'] ?? '';

// 1. Verify Authorization
if ($authKey !== $SECRET_KEY) {
    http_response_code(401);
    echo json_encode(['success' => false, 'message' => 'Unauthorized']);
    exit;
}

// 2. Database Connection
$pdo = new PDO('mysql:host=localhost;dbname=your_db', 'db_user', 'db_password', [
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC
]);

// 3. Handle Actions
switch ($action) {
    case 'pull':
        // Fetch up to 10 pending tasks
        $stmt = $pdo->query("SELECT id, number, message, sim_slot, delay FROM sms_queue WHERE status = 'pending' LIMIT 10");
        $tasks = $stmt->fetchAll();
        
        // Mark fetched tasks as processing
        if (!empty($tasks)) {
            $ids = implode(',', array_column($tasks, 'id'));
            $pdo->query("UPDATE sms_queue SET status = 'processing' WHERE id IN ($ids)");
        }
        
        echo json_encode(['success' => true, 'tasks' => $tasks]);
        break;

    case 'update_status':
        $taskId = (int)($data['task_id'] ?? 0);
        $status = in_array($data['status'], ['sent', 'failed']) ? $data['status'] : 'failed';
        
        $stmt = $pdo->prepare("UPDATE sms_queue SET status = ?, sent_at = NOW() WHERE id = ?");
        $stmt->execute([$status, $taskId]);

        echo json_encode(['success' => true, 'message' => 'Status updated']);
        break;

    case 'push':
        $numbers = explode(',', $data['numbers'] ?? '');
        $message = trim($data['message'] ?? '');
        $simSlot = (int)($data['sim_slot'] ?? 0);
        $delay   = (int)($data['delay'] ?? 2);
        
        $taskIds = [];
        $stmt = $pdo->prepare("INSERT INTO sms_queue (number, message, sim_slot, delay, status) VALUES (?, ?, ?, ?, 'pending')");
        
        foreach ($numbers as $num) {
            $num = trim($num);
            if (!empty($num) && !empty($message)) {
                $stmt->execute([$num, $message, $simSlot, $delay]);
                $taskIds[] = $pdo->lastInsertId();
            }
        }

        echo json_encode(['success' => true, 'message' => 'Tasks added', 'task_ids' => $taskIds]);
        break;

    default:
        echo json_encode(['success' => false, 'message' => 'Unknown action']);
        break;
}
```

---

## 🎯 Where Can You Integrate This?

You can connect your custom Netfie Gateway backend into virtually anything:

| Industry / Application | Integration Use-Case |
|---|---|
| **E-Commerce (WooCommerce, Shopify, Custom Laravel)** | Order confirmations, COD phone verification, tracking alerts, abandoned cart reminders |
| **Authentication & Security** | Fast login OTPs, 2-Factor Authentication (2FA), password reset tokens |
| **Hospitals, Clinics & Diagnostic Labs** | Appointment reminders, lab test report ready notifications |
| **Schools, Colleges & Coaching Centers** | Daily student attendance alerts to parents, exam results, tuition fee dues reminders |
| **ISP & Cable Billing Systems (MikroTik / Radius)** | Monthly internet bill notices, payment reminders, disconnection warnings |
| **POS & Accounting Software** | Send instant digital receipts to customer mobile numbers after checkout |
| **Courier & Logistics Portals** | Merchant parcel tracking updates, driver assignment, OTP delivery handovers |
| **CRM & Lead Management (Perfex, HubSpot, Zoho)** | Instant alert to sales team when a lead signs up; bulk promotional campaigns |
| **Server & DevOps Alerts (Uptime Kuma, Zabbix)** | Critical server down alerts sent directly to phone even if internet is disconnected |

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
