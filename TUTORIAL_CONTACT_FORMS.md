# 📋 Contact Form 7 & Elementor Forms Auto-SMS Tutorial
### *Instant SMS Alerts on Lead Generation & Inquiries via Netfie Gateway*

Whenever a customer fills out an inquiry or quote request on your WordPress website, you can send an instant confirmation SMS to the customer, and an instant alert to your sales manager's phone.

---

## 1. Contact Form 7 Integration

Add this snippet to your theme's `functions.php`:

```php
add_action('wpcf7_mail_sent', 'netfie_cf7_auto_sms');

function netfie_cf7_auto_sms($contact_form) {
    $submission = WPCF7_Submission::get_instance();
    if (!$submission) return;

    $posted_data = $submission->get_posted_data();

    // Field names match your CF7 tags: [your-name], [your-phone], [your-message]
    $customer_name  = $posted_data['your-name'] ?? 'Customer';
    $customer_phone = $posted_data['your-phone'] ?? '';

    if (empty($customer_phone)) return;

    $apiUrl = get_rest_url(null, 'netfie/v1/gateway');
    $apiKey = get_option('netfie_sms_api_key');

    // 1. Send Instant Confirmation to Customer
    $customer_msg = "Hello {$customer_name}, thank you for contacting us! We have received your inquiry and will call you back shortly.";

    wp_remote_post($apiUrl, [
        'method'  => 'POST',
        'timeout' => 10,
        'body'    => [
            'auth_key' => $apiKey,
            'action'   => 'push',
            'numbers'  => $customer_phone,
            'message'  => $customer_msg,
            'sim_slot' => 0,
            'delay'    => 2
        ]
    ]);

    // 2. Alert Business Owner / Sales Manager
    $admin_phone = '01711111111'; // Your manager's phone number
    $admin_msg   = "New Lead: {$customer_name} ({$customer_phone}) submitted a contact form.";

    wp_remote_post($apiUrl, [
        'method'  => 'POST',
        'timeout' => 10,
        'body'    => [
            'auth_key' => $apiKey,
            'action'   => 'push',
            'numbers'  => $admin_phone,
            'message'  => $admin_msg,
            'sim_slot' => 0,
            'delay'    => 2
        ]
    ]);
}
```

---

## 2. Elementor Pro Forms Integration

Add this snippet to your theme's `functions.php`:

```php
add_action('elementor_pro/forms/new_record', 'netfie_elementor_form_auto_sms', 10, 2);

function netfie_elementor_form_auto_sms($record, $handler) {
    // Get form field values
    $raw_fields = $record->get('fields');
    $fields = [];
    foreach ($raw_fields as $id => $field) {
        $fields[$id] = $field['value'];
    }

    $customer_phone = $fields['phone'] ?? $fields['mobile'] ?? '';
    $customer_name  = $fields['name'] ?? 'Customer';

    if (empty($customer_phone)) return;

    $message = "Hi {$customer_name}, we received your quote request and will contact you promptly!";

    wp_remote_post(get_rest_url(null, 'netfie/v1/gateway'), [
        'method'  => 'POST',
        'timeout' => 10,
        'body'    => [
            'auth_key' => get_option('netfie_sms_api_key'),
            'action'   => 'push',
            'numbers'  => $customer_phone,
            'message'  => $message,
            'sim_slot' => 0,
            'delay'    => 2
        ]
    ]);
}
```
