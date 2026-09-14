<div align="center">

<a href="https://netfie.com" target="_blank">
  <img src="https://netfie.com/wp-content/uploads/2025/03/Netfie__1_-removebg-preview-450x174.png.webp" alt="Netfie Official Agency Logo" width="300">
</a>

<br><br>

<a href="https://play.google.com/store/apps/details?id=com.netfie.netfiebulksmssender.netfiebulksmssender" target="_blank">
  <img src="https://raw.githubusercontent.com/debarjunmaharaj/Netfie-Buik-sms-sender-web/refs/heads/main/icon.png" alt="Netfie App Logo" width="110" style="border-radius: 22px; box-shadow: 0 6px 20px rgba(0,0,0,0.12);">
</a>

# 🛒 WordPress & WooCommerce Complete Tutorial
### *Automate Order SMS, OTP Verification, Status Alerts & Customer Reminders via Android Gateway*

[![Download Android App](https://img.shields.io/badge/Google_Play-Download%20Netfie%20App-34A853?style=for-the-badge&logo=google-play&logoColor=white)](https://play.google.com/store/apps/details?id=com.netfie.netfiebulksmssender.netfiebulksmssender)
[![Netfie Website](https://img.shields.io/badge/Netfie-Official%20Site-0070f3?style=for-the-badge&logo=google-chrome&logoColor=white)](https://netfie.com)
[![WhatsApp Support](https://img.shields.io/badge/WhatsApp-01884189495-25D366?style=for-the-badge&logo=whatsapp&logoColor=white)](https://wa.me/8801884189495)

</div>

---

## 📖 What You Will Learn

This step-by-step tutorial teaches you how to turn your WordPress & WooCommerce store into an automated SMS notifications hub using your own phone and the **Netfie Bulk SMS Gateway**:

1. [Installing & Configuring the Netfie WordPress Plugin](#1-installing--configuring-the-plugin)
2. [Connecting Your Android Smartphone / Modem](#2-connecting-your-android-smartphone)
3. [Auto SMS on New WooCommerce Orders (Pending / Processing)](#3-auto-sms-on-new-woocommerce-orders)
4. [Auto SMS on Order Status Transitions (Completed / Shipped / Cancelled)](#4-auto-sms-on-order-status-transitions)
5. [Phone Number Validation & Sanitization (Bangladesh Format 01XXXXXXXXX)](#5-phone-number-sanitization)
6. [Preventing Fake COD Orders with OTP Verification](#6-otp-verification-for-cod-orders)
7. [Abandoned Cart Recovery via SMS](#7-abandoned-cart-recovery-via-sms)
8. [Embedding Customer SMS Dashboard using Shortcodes](#8-shortcodes--customer-dashboard)
9. [Troubleshooting & Best Practices](#9-troubleshooting--best-practices)

---

## 1. Installing & Configuring the Plugin

### Step 1: Install the Plugin
1. Download the `netfie-sms-product.zip` plugin.
2. In WordPress Admin, navigate to **Plugins → Add New → Upload Plugin**.
3. Select `netfie-sms-product.zip` and click **Install Now**, then **Activate Plugin**.

### Step 2: Configure Settings
1. Go to **WP Admin → Netfie SMS → Settings**.
2. Set your **Secret API Key** (or click *Generate Key* to create a secure 32-character token).
3. Note your **Gateway URL**:
   ```
   https://yourdomain.com/wp-json/netfie/v1/gateway
   ```
4. Configure default SIM slot (`SIM 1` or `SIM 2`) and interval pacing (recommended: `2` to `3` seconds).
5. Click **Save Settings**.

---

## 2. Connecting Your Android Smartphone

1. Download & install the **Netfie Bulk SMS Gateway & Mobile Remote** app from Google Play:  
   👉 [Google Play Store Link](https://play.google.com/store/apps/details?id=com.netfie.netfiebulksmssender.netfiebulksmssender)
2. Open the app on your phone.
3. Tap **Server Settings**:
   - **Gateway URL**: `https://yourdomain.com/wp-json/netfie/v1/gateway`
   - **API Key**: Enter the secret key configured in WP Admin.
4. Tap **Connect**.
5. Grant required Android permissions (SMS, Notification, Battery Optimization whitelist).
6. Verify device in WordPress: Go to **Netfie SMS → Devices**. Your phone will appear online with battery and network status.

---

## 3. Auto SMS on New WooCommerce Orders

Add this snippet to your child theme's `functions.php` or inside a custom mini-plugin:

```php
/**
 * Send SMS to customer immediately upon new WooCommerce order placement.
 */
add_action('woocommerce_thankyou', 'netfie_send_new_order_customer_sms', 20, 1);

function netfie_send_new_order_customer_sms($order_id) {
    if (!$order_id) return;

    // Prevent duplicate SMS on page reload
    if (get_post_meta($order_id, '_netfie_order_sms_sent', true)) {
        return;
    }

    $order = wc_get_order($order_id);
    if (!$order) return;

    $phone     = $order->get_billing_phone();
    $firstName = $order->get_billing_first_name();
    $total     = wc_price($order->get_total());

    if (empty($phone)) return;

    // Construct SMS Text
    $message = "Dear {$firstName}, thank you for ordering with us! Your Order #{$order_id} has been received. Total: {$order->get_total()} BDT. We are preparing it for shipment.";

    // Push to Netfie Gateway
    $response = wp_remote_post(get_rest_url(null, 'netfie/v1/gateway'), [
        'method'  => 'POST',
        'timeout' => 15,
        'body'    => [
            'auth_key' => get_option('netfie_sms_api_key', 'YOUR_FALLBACK_KEY'),
            'action'   => 'push',
            'numbers'  => $phone,
            'message'  => $message,
            'sim_slot' => 0,
            'delay'    => 2
        ]
    ]);

    if (!is_wp_error($response)) {
        update_post_meta($order_id, '_netfie_order_sms_sent', 'yes');
        $order->add_order_note("Netfie SMS: Order confirmation dispatched to {$phone}");
    }
}
```

---

## 4. Auto SMS on Order Status Transitions

Notify customers automatically when order status changes to **Shipped / Completed**, **Cancelled**, or **On Hold**:

```php
/**
 * Send SMS when order status changes to Completed.
 */
add_action('woocommerce_order_status_completed', 'netfie_send_order_completed_sms', 10, 1);

function netfie_send_order_completed_sms($order_id) {
    $order = wc_get_order($order_id);
    if (!$order) return;

    $phone = $order->get_billing_phone();
    $name  = $order->get_billing_first_name();
    if (empty($phone)) return;

    $message = "Hello {$name}, your Order #{$order_id} is completed and on its way! Thank you for shopping with us.";

    wp_remote_post(get_rest_url(null, 'netfie/v1/gateway'), [
        'method'  => 'POST',
        'timeout' => 12,
        'body'    => [
            'auth_key' => get_option('netfie_sms_api_key'),
            'action'   => 'push',
            'numbers'  => $phone,
            'message'  => $message,
            'sim_slot' => 0,
            'delay'    => 2
        ]
    ]);

    $order->add_order_note("Netfie SMS: Order completion notice sent to {$phone}");
}
```

---

## 5. Phone Number Sanitization (Bangladesh Format)

Ensure all customer phone numbers conform to carrier standards (`+8801XXXXXXXXX` or `01XXXXXXXXX`):

```php
function netfie_sanitize_bd_phone($phone) {
    // Remove spaces, hyphens, and parentheses
    $clean = preg_replace('/[^0-9]/', '', $phone);

    // If starts with 880, strip to local format or standardize
    if (str_starts_with($clean, '8801') && strlen($clean) === 13) {
        return '0' . substr($clean, 3);
    }

    // Standard 11 digit 01XXXXXXXXX
    if (str_starts_with($clean, '01') && strlen($clean) === 11) {
        return $clean;
    }

    return $phone;
}
```

---

## 6. OTP Verification for COD Orders

Prevent fake orders and return parcel courier costs by sending a 4-digit or 6-digit OTP to verify Cash on Delivery:

```php
/**
 * Generate and dispatch checkout verification OTP
 */
function netfie_dispatch_checkout_otp($phone) {
    $otp = rand(1000, 9999);
    set_transient('netfie_otp_' . md5($phone), $otp, 300); // 5 minutes validity

    $message = "Your Netfie verification code is {$otp}. Please enter this code to confirm your order.";

    wp_remote_post(get_rest_url(null, 'netfie/v1/gateway'), [
        'method'  => 'POST',
        'timeout' => 10,
        'body'    => [
            'auth_key' => get_option('netfie_sms_api_key'),
            'action'   => 'push',
            'numbers'  => $phone,
            'message'  => $message,
            'sim_slot' => 0,
            'delay'    => 0
        ]
    ]);

    return true;
}
```

---

## 7. Abandoned Cart Recovery via SMS

Send a friendly reminder with a discount coupon when customers leave items in their cart:

```php
/**
 * Example Abandoned Cart Reminder
 */
function netfie_send_cart_recovery_sms($customer_phone, $cart_url, $coupon_code = 'SAVE10') {
    $message = "You left items in your cart! Complete your purchase today with 10% OFF using coupon {$coupon_code}: {$cart_url}";

    wp_remote_post(get_rest_url(null, 'netfie/v1/gateway'), [
        'method'  => 'POST',
        'timeout' => 12,
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

---

## 8. Shortcodes & Customer Dashboard

The **Netfie SMS Product Plugin** comes with built-in shortcodes to present the SMS sending console on any WordPress page:

| Shortcode | Purpose | Access Control |
|---|---|---|
| `[netfie-sms-dashboard]` | Renders the full 2-column responsive Bulk SMS console | Purchasers of SMS Product & Admins only |
| `[netfie-sms-dashbord]` | Alias for backward compatibility | Purchasers & Admins only |
| `[netfie-sms-button]` | Renders a styled "Bulk SMS Sender" navigation button | Logged-in customers with active purchase |

### Adding Dashboard to Custom Page:
1. Go to **Pages → Add New**.
2. Title: `My SMS Remote`.
3. In the content editor, insert: `[netfie-sms-dashboard]`.
4. Publish the page.
5. In **Netfie SMS → Settings**, paste your new page URL into **Custom Dashboard Page URL**. WooCommerce My Account order links will now automatically point to this custom URL.

---

## 9. Troubleshooting & Best Practices

1. **Keep App in Background**: Make sure to disable aggressive battery optimization for the Netfie app on your Android device (Settings → Battery → Battery Optimization → Netfie → Don't optimize).
2. **SIM Card Selection**: Set `sim_slot = 0` for SIM 1 or `sim_slot = 1` for SIM 2 based on which card holds your SMS package.
3. **Pacing Delay**: Use a `delay` of `2` to `3` seconds between messages when broadcasting large campaigns to prevent telecom network throttling.
4. **Log Inspection**: In WP Admin, visit **Netfie SMS → Queue** to monitor delivery rates and inspect failed numbers.

---

## 👨‍💻 Need Custom WooCommerce Customization?

Need custom checkout OTP flows, CRM synchronization, or multi-vendor SMS routing? Contact our engineering team:

- **Agency**: **Netfie** (Authorized Web Development Partner) — [netfie.com](https://netfie.com)
- **Lead Developer**: **Debarjun Chakraborty**
- **Developer Portfolio**: [boost4all.com/@debarjunofficial](https://boost4all.com/@debarjunofficial)
- **WhatsApp**: [01884189495](https://wa.me/8801884189495)
- **Direct Phone**: [01772326146](tel:01772326146)
- **Email**: `netfieofficial@gmail.com`
