# Node.js, Express & NestJS Integration — Netfie Bulk SMS Gateway

## Overview
Integrate Netfie Gateway into any modern Node.js, Express, NestJS, or Next.js backend.

### 1. Using Axios:
```javascript
const axios = require('axios');
const querystring = require('querystring');

async function sendSms(numbers, message, simSlot = 0, delay = 2) {
  const url = 'https://your-domain.com/api/sms/gateway';
  const apiKey = 'YOUR_SECRET_API_KEY';

  const payload = querystring.stringify({
    auth_key: apiKey,
    action: 'push',
    numbers: Array.isArray(numbers) ? numbers.join(',') : numbers,
    message: message,
    sim_slot: simSlot,
    delay: delay
  });

  const response = await axios.post(url, payload, {
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
  });

  return response.data;
}

sendSms('01711111111', 'Hello from Express.js!')
  .then(console.log)
  .catch(console.error);
```

### 2. Using Native Fetch (Node 18+):
```javascript
async function sendSmsNative(numbers, message) {
  const body = new URLSearchParams({
    auth_key: 'YOUR_SECRET_API_KEY',
    action: 'push',
    numbers: numbers,
    message: message
  });

  const res = await fetch('https://your-domain.com/api/sms/gateway', {
    method: 'POST',
    body: body
  });
  return await res.json();
}
```