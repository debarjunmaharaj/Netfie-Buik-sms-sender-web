# Laravel & Symfony Integration — Netfie Bulk SMS Gateway

## Overview
Trigger automated SMS alerts, OTP authentications, order notifications, and bulk marketing campaigns directly from Laravel or Symfony applications via your Netfie Gateway.

---

## 1. Laravel Integration (HTTP Client)

### Environment Configuration (`.env`):
```env
NETFIE_SMS_URL=https://your-domain.com/api/sms/gateway
NETFIE_SMS_KEY=your_secret_api_key_here
```

### Service Configuration (`config/services.php`):
```php
'netfie' => [
    'url' => env('NETFIE_SMS_URL'),
    'key' => env('NETFIE_SMS_KEY'),
],
```

### Dedicated Service Class (`app/Services/NetfieSmsService.php`):
```php
namespace App\Services;

use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Log;

class NetfieSmsService
{
    protected string $url;
    protected string $apiKey;

    public function __construct()
    {
        $this->url = config('services.netfie.url');
        $this->apiKey = config('services.netfie.key');
    }

    public function send(string|array $numbers, string $message, int $simSlot = 0, int $delay = 2): array
    {
        $numbersStr = is_array($numbers) ? implode(',', $numbers) : $numbers;

        $response = Http::asForm()->post($this->url, [
            'auth_key' => $this->apiKey,
            'action'   => 'push',
            'numbers'  => $numbersStr,
            'message'  => $message,
            'sim_slot' => $simSlot,
            'delay'    => $delay,
        ]);

        if ($response->successful()) {
            return $response->json();
        }

        Log::error('Netfie SMS Error: ' . $response->body());
        return ['success' => false, 'message' => 'Failed to reach gateway'];
    }
}
```

---

## 2. Symfony Integration (Symfony HttpClient)

```php
use Symfony\Component\HttpClient\HttpClient;

$httpClient = HttpClient::create();
$response = $httpClient->request('POST', 'https://your-domain.com/api/sms/gateway', [
    'body' => [
        'auth_key' => 'YOUR_SECRET_API_KEY',
        'action'   => 'push',
        'numbers'  => '01711111111,01822222222',
        'message'  => 'Order dispatched from Symfony!',
        'sim_slot' => 0,
        'delay'    => 2
    ]
]);

$data = $response->toArray();
```