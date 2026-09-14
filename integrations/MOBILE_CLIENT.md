# Flutter & React Native Client Integration — Netfie Bulk SMS Gateway

## 1. Flutter (Dart)
```dart
import 'package:http/http.dart' as http;
import 'dart:convert';

Future<Map<String, dynamic>> sendSms({
  required String numbers,
  required String message,
}) async {
  final url = Uri.parse('https://your-domain.com/api/sms/gateway');
  final response = await http.post(url, body: {
    'auth_key': 'YOUR_SECRET_API_KEY',
    'action': 'push',
    'numbers': numbers,
    'message': message,
    'sim_slot': '0',
    'delay': '2',
  });

  return jsonDecode(response.body);
}
```

## 2. React Native (JavaScript)
```javascript
export async function sendSms(numbers, message) {
  const form = new FormData();
  form.append('auth_key', 'YOUR_SECRET_API_KEY');
  form.append('action', 'push');
  form.append('numbers', numbers);
  form.append('message', message);

  const res = await fetch('https://your-domain.com/api/sms/gateway', {
    method: 'POST',
    body: form
  });

  return await res.json();
}
```