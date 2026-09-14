# WordPress & WooCommerce Integration — Netfie Bulk SMS Gateway

## Overview
Send instant automated SMS notifications on WooCommerce order placement or status changes (e.g., Processing, Completed, Shipped) using WordPress's native `wp_remote_post()`.

```php
add_action('woocommerce_order_status_processing', 'netfie_send_order_sms', 10, 1);

function netfie_send_order_sms($order_id) {
    $order = wc_get_order($order_id);
    if (!$order) return;

    $phone = $order->get_billing_phone();
    $name  = $order->get_billing_first_name();

    if (empty($phone)) return;

    $message = "Hello {$name}, thank you for your order #{$order_id}! We have received it and are preparing it for delivery.";

    wp_remote_post('https://your-domain.com/wp-json/netfie/v1/gateway', [
        'method'    => 'POST',
        'timeout'   => 15,
        'body'      => [
            'auth_key' => 'YOUR_SECRET_API_KEY',
            'action'   => 'push',
            'numbers'  => $phone,
            'message'  => $message,
            'sim_slot' => 0,
            'delay'    => 2
        ]
    ]);
}
```