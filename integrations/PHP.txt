# Pure & Core PHP Integration — Netfie Bulk SMS Gateway

## Overview
Connect any native PHP script, custom CMS, or legacy codebase without requiring Composer or third-party libraries.

### Standard cURL Helper:
```php
<?php
function sendNetfieSms($numbers, $message, $simSlot = 0, $delay = 2) {
    $apiUrl = 'https://your-domain.com/api/sms/gateway';
    $apiKey = 'YOUR_SECRET_API_KEY';

    $payload = [
        'auth_key' => $apiKey,
        'action'   => 'push',
        'numbers'  => is_array($numbers) ? implode(',', $numbers) : $numbers,
        'message'  => $message,
        'sim_slot' => (int)$simSlot,
        'delay'    => (int)$delay,
    ];

    $ch = curl_init($apiUrl);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($payload));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
    curl_setopt($ch, CURLOPT_TIMEOUT, 15);

    $response = curl_exec($ch);
    $error = curl_error($ch);
    curl_close($ch);

    if ($error) {
        return ['success' => false, 'error' => $error];
    }

    return json_decode($response, true);
}

// Example Usage
$result = sendNetfieSms('01711111111,01822222222', 'Test alert from Core PHP');
print_r($result);
?>
```