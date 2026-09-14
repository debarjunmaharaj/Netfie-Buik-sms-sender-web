# Google Sheets (Google Apps Script) — Netfie Bulk SMS Gateway

## Overview
Send bulk SMS directly from Google Sheets! Ideal for office staff, schools, and non-technical marketing teams.

### Google Apps Script Code:
1. Open your Google Sheet.
2. Click **Extensions → Apps Script**.
3. Paste this code and click **Run**:

```javascript
function sendBulkSMSFromSheet() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var rows = sheet.getDataRange().getValues();

  var apiUrl = 'https://your-domain.com/api/sms/gateway';
  var apiKey = 'YOUR_SECRET_API_KEY';

  // Column A: Phone Number, Column B: Message, Column C: Status
  for (var i = 1; i < rows.length; i++) {
    var phone = rows[i][0];
    var message = rows[i][1];
    var status = rows[i][2];

    if (phone && message && status !== 'SENT') {
      var payload = {
        'auth_key': apiKey,
        'action': 'push',
        'numbers': phone.toString(),
        'message': message,
        'sim_slot': '0',
        'delay': '2'
      };

      var options = {
        'method': 'post',
        'payload': payload,
        'muteHttpExceptions': true
      };

      UrlFetchApp.fetch(apiUrl, options);
      sheet.getRange(i + 1, 3).setValue('SENT');
      Utilities.sleep(1500); // 1.5 second pacing
    }
  }
}
```