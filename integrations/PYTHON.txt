# Python, Django & FastAPI Integration — Netfie Bulk SMS Gateway

## Overview
Integrate SMS dispatching into Django models, Celery tasks, or FastAPI asynchronous endpoints.

```python
import requests

def send_netfie_sms(numbers, message, sim_slot=0, delay=2):
    url = 'https://your-domain.com/api/sms/gateway'
    payload = {
        'auth_key': 'YOUR_SECRET_API_KEY',
        'action': 'push',
        'numbers': ','.join(numbers) if isinstance(numbers, list) else numbers,
        'message': message,
        'sim_slot': sim_slot,
        'delay': delay
    }

    try:
        response = requests.post(url, data=payload, timeout=12)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.RequestException as e:
        return {'success': False, 'error': str(e)}

# Quick test
if __name__ == '__main__':
    result = send_netfie_sms(['01711111111', '01822222222'], 'Hello from Python FastAPI!')
    print(result)
```