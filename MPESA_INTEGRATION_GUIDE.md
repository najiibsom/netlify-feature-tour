# M-Pesa Integration Guide for ShopLocal

## Overview
This guide provides complete instructions for integrating M-Pesa payments into your SHEIN-style shopping app for local sellers. The integration includes STK Push, payment validation, and refund processing.

## 🔧 Technical Requirements

### M-Pesa API Credentials
You'll need to obtain the following from Safaricom:
- **Consumer Key**: Your app's unique identifier
- **Consumer Secret**: Your app's secret key
- **Business Short Code**: Your business number (e.g., 174379)
- **Passkey**: Used for generating security credentials
- **Callback URL**: Where M-Pesa will send payment notifications

### Environment Setup
```bash
# Install required packages
npm install axios crypto-js moment
```

## 📱 STK Push Implementation

### 1. Generate Access Token
```javascript
async function generateMPesaToken() {
  const auth = Buffer.from(`${CONSUMER_KEY}:${CONSUMER_SECRET}`).toString('base64');
  
  const response = await axios.post(
    'https://sandbox.safaricom.co.ke/oauth/v1/generate?grant_type=client_credentials',
    {},
    {
      headers: {
        Authorization: `Basic ${auth}`,
        'Content-Type': 'application/json'
      }
    }
  );
  
  return response.data.access_token;
}
```

### 2. Generate Security Credential
```javascript
function generatePassword() {
  const timestamp = moment().format('YYYYMMDDHHmmss');
  const password = Buffer.from(BUSINESS_SHORT_CODE + PASSKEY + timestamp).toString('base64');
  return { password, timestamp };
}
```

### 3. STK Push Request
```javascript
async function initiateSTKPush(phoneNumber, amount, orderReference) {
  const token = await generateMPesaToken();
  const { password, timestamp } = generatePassword();
  
  const stkPushData = {
    BusinessShortCode: BUSINESS_SHORT_CODE,
    Password: password,
    Timestamp: timestamp,
    TransactionType: 'CustomerPayBillOnline',
    Amount: amount,
    PartyA: phoneNumber,
    PartyB: BUSINESS_SHORT_CODE,
    PhoneNumber: phoneNumber,
    CallBackURL: 'https://yourdomain.com/api/mpesa/callback',
    AccountReference: orderReference,
    TransactionDesc: 'Payment for order ' + orderReference
  };
  
  const response = await axios.post(
    'https://sandbox.safaricom.co.ke/mpesa/stkpush/v1/processrequest',
    stkPushData,
    {
      headers: {
        Authorization: `Bearer ${token}`,
        'Content-Type': 'application/json'
      }
    }
  );
  
  return response.data;
}
```

## 🔄 Payment Callback Handling

### Callback Endpoint
```javascript
// Express.js endpoint example
app.post('/api/mpesa/callback', (req, res) => {
  const { Body } = req.body;
  
  if (Body.stkCallback.ResultCode === 0) {
    // Payment successful
    const { CheckoutRequestID, Amount, MpesaReceiptNumber, PhoneNumber } = Body.stkCallback;
    
    // Process successful payment
    processSuccessfulPayment({
      checkoutRequestId: CheckoutRequestID,
      amount: Amount,
      mpesaReceiptNumber: MpesaReceiptNumber,
      phoneNumber: PhoneNumber
    });
    
    // Send confirmation SMS/WhatsApp
    sendPaymentConfirmation(PhoneNumber, MpesaReceiptNumber);
    
  } else {
    // Payment failed
    console.log('Payment failed:', Body.stkCallback.ResultDesc);
    handleFailedPayment(Body.stkCallback);
  }
  
  res.json({ success: true });
});
```

## 💰 Refund Processing

### B2C Refund Implementation
```javascript
async function processRefund(phoneNumber, amount, orderReference) {
  const token = await generateMPesaToken();
  const { password, timestamp } = generatePassword();
  
  const refundData = {
    InitiatorName: 'your_initiator_name',
    SecurityCredential: password,
    CommandID: 'BusinessPayment',
    Amount: amount,
    PartyA: BUSINESS_SHORT_CODE,
    PartyB: phoneNumber,
    Remarks: 'Refund for order ' + orderReference,
    QueueTimeOutURL: 'https://yourdomain.com/api/mpesa/timeout',
    ResultURL: 'https://yourdomain.com/api/mpesa/refund-result',
    Occasion: 'Refund'
  };
  
  const response = await axios.post(
    'https://sandbox.safaricom.co.ke/mpesa/b2c/v1/paymentrequest',
    refundData,
    {
      headers: {
        Authorization: `Bearer ${token}`,
        'Content-Type': 'application/json'
      }
    }
  );
  
  return response.data;
}
```

## 🛡️ Security Best Practices

### 1. Validate Callback Requests
```javascript
function validateMPesaCallback(req) {
  // Verify IP whitelist
  const allowedIPs = ['196.201.214.200', '196.201.214.206', '196.201.213.114'];
  const clientIP = req.ip;
  
  if (!allowedIPs.includes(clientIP)) {
    throw new Error('Invalid IP address');
  }
  
  // Additional validation logic
  return true;
}
```

### 2. Encrypt Sensitive Data
```javascript
const crypto = require('crypto');

function encryptSensitiveData(data) {
  const algorithm = 'aes-256-cbc';
  const key = process.env.ENCRYPTION_KEY;
  const iv = crypto.randomBytes(16);
  
  const cipher = crypto.createCipher(algorithm, key);
  let encrypted = cipher.update(data, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  
  return encrypted;
}
```

## 📊 Database Schema

### Orders Table
```sql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    customer_id BIGINT NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL,
    status ENUM('pending', 'confirmed', 'shipped', 'delivered', 'cancelled') DEFAULT 'pending',
    payment_method ENUM('mpesa', 'card', 'cod') NOT NULL,
    payment_status ENUM('pending', 'completed', 'failed', 'refunded') DEFAULT 'pending',
    mpesa_receipt_number VARCHAR(50),
    checkout_request_id VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### M-Pesa Transactions Table
```sql
CREATE TABLE mpesa_transactions (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    order_id BIGINT NOT NULL,
    transaction_type ENUM('payment', 'refund') NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    phone_number VARCHAR(15) NOT NULL,
    mpesa_receipt_number VARCHAR(50),
    checkout_request_id VARCHAR(100),
    result_code INT,
    result_desc TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (order_id) REFERENCES orders(id)
);
```

## 🚀 Frontend Integration

### Customer Payment Flow
```javascript
// Frontend - Initiate payment
async function initiatePayment(orderData) {
  try {
    const response = await fetch('/api/payments/initiate', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        phoneNumber: orderData.phoneNumber,
        amount: orderData.total,
        orderReference: orderData.orderReference
      })
    });
    
    const result = await response.json();
    
    if (result.success) {
      // Show STK push notification
      showSTKPushNotification(result.checkoutRequestId);
      
      // Poll for payment status
      pollPaymentStatus(result.checkoutRequestId);
    }
  } catch (error) {
    console.error('Payment initiation failed:', error);
  }
}

// Poll payment status
async function pollPaymentStatus(checkoutRequestId) {
  const maxAttempts = 60; // 2 minutes
  let attempts = 0;
  
  const interval = setInterval(async () => {
    attempts++;
    
    const response = await fetch(`/api/payments/status/${checkoutRequestId}`);
    const result = await response.json();
    
    if (result.status === 'completed') {
      clearInterval(interval);
      handlePaymentSuccess(result);
    } else if (result.status === 'failed' || attempts >= maxAttempts) {
      clearInterval(interval);
      handlePaymentFailure(result);
    }
  }, 2000);
}
```

## 📱 SMS/WhatsApp Notifications

### Payment Confirmation
```javascript
async function sendPaymentConfirmation(phoneNumber, receiptNumber) {
  const message = `✅ Payment confirmed! Receipt: ${receiptNumber}. Your order is being processed. Track at: https://shoplocal.co.ke/track`;
  
  // Send SMS
  await sendSMS(phoneNumber, message);
  
  // Send WhatsApp
  await sendWhatsApp(phoneNumber, message);
}

async function sendSMS(phoneNumber, message) {
  // Using Africa's Talking SMS service
  const response = await fetch('https://api.africastalking.com/version1/messaging', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
      'apiKey': process.env.SMS_API_KEY
    },
    body: new URLSearchParams({
      username: 'sandbox',
      to: phoneNumber,
      message: message
    })
  });
}
```

## 🔍 Testing & Debugging

### Test Credentials (Sandbox)
```javascript
const SANDBOX_CONFIG = {
  consumerKey: 'your_sandbox_consumer_key',
  consumerSecret: 'your_sandbox_consumer_secret',
  businessShortCode: '174379',
  passkey: 'your_sandbox_passkey',
  baseURL: 'https://sandbox.safaricom.co.ke'
};
```

### Test Phone Numbers
- **254708374149**: Test number for successful payments
- **254708374150**: Test number for failed payments

### Common Error Codes
- **0**: Success
- **1**: Insufficient funds
- **2**: Less than minimum transaction value
- **3**: More than maximum transaction value
- **4**: Would exceed daily transfer limit
- **5**: Would exceed minimum balance
- **6**: Unresolved primary party
- **7**: Unresolved receiver party
- **8**: Would exceed maximum balance

## 📈 Analytics & Reporting

### Payment Analytics
```javascript
async function getPaymentAnalytics(startDate, endDate) {
  const analytics = await db.query(`
    SELECT 
      DATE(created_at) as date,
      COUNT(*) as total_transactions,
      SUM(amount) as total_amount,
      COUNT(CASE WHEN result_code = 0 THEN 1 END) as successful_transactions,
      COUNT(CASE WHEN result_code != 0 THEN 1 END) as failed_transactions
    FROM mpesa_transactions 
    WHERE created_at BETWEEN ? AND ?
    GROUP BY DATE(created_at)
    ORDER BY date DESC
  `, [startDate, endDate]);
  
  return analytics;
}
```

## 🛠️ Deployment Considerations

### Environment Variables
```env
# M-Pesa Configuration
MPESA_CONSUMER_KEY=your_consumer_key
MPESA_CONSUMER_SECRET=your_consumer_secret
MPESA_BUSINESS_SHORT_CODE=your_short_code
MPESA_PASSKEY=your_passkey
MPESA_BASE_URL=https://api.safaricom.co.ke  # Production
# MPESA_BASE_URL=https://sandbox.safaricom.co.ke  # Sandbox

# Database
DATABASE_URL=your_database_url

# SMS/WhatsApp
SMS_API_KEY=your_sms_api_key
WHATSAPP_TOKEN=your_whatsapp_token

# Security
ENCRYPTION_KEY=your_encryption_key
JWT_SECRET=your_jwt_secret
```

### Production Checklist
- [ ] Update to production M-Pesa endpoints
- [ ] Implement proper error handling
- [ ] Set up monitoring and alerts
- [ ] Configure SSL certificates
- [ ] Implement rate limiting
- [ ] Set up backup and recovery
- [ ] Configure CI/CD pipeline
- [ ] Set up logging and monitoring

## 📞 Support & Resources

### Safaricom Developer Portal
- **Documentation**: https://developer.safaricom.co.ke
- **API Reference**: https://developer.safaricom.co.ke/apis
- **Support**: developer@safaricom.co.ke

### Community Resources
- **GitHub**: https://github.com/safaricom/mpesa-php-sdk
- **Stack Overflow**: Tag `mpesa-api`
- **Developer Forums**: https://developer.safaricom.co.ke/forum

## 🐛 Common Issues & Solutions

### Issue: STK Push not appearing on phone
**Solution**: Ensure phone number is in format 254XXXXXXXX (not 07XXXXXXXX)

### Issue: Callback URL not receiving data
**Solution**: Verify your callback URL is publicly accessible and returns 200 status

### Issue: Token expiry
**Solution**: Implement token refresh mechanism before each API call

### Issue: Payment timeout
**Solution**: Implement proper timeout handling and retry logic

---

This guide provides a comprehensive foundation for implementing M-Pesa payments in your shopping application. Remember to thoroughly test in sandbox environment before going live, and always follow security best practices when handling financial transactions.