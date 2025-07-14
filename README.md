# 🛍️ ShopLocal - SHEIN-Style Shopping App with M-Pesa Integration

A comprehensive e-commerce platform designed for local sellers in Kenya, featuring M-Pesa payments, customer portal, and admin management system.

## ✨ Features

### 💸 **Payment System**
- **M-Pesa Integration**: STK Push, payment validation, and refund processing
- **Multiple Payment Options**: M-Pesa, Card Payments, and Cash on Delivery
- **Secure Transactions**: Fraud detection and transaction confirmation
- **Digital Receipts**: Auto-generated receipts with order history
- **SMS/WhatsApp Notifications**: Payment confirmations and order updates

### 👤 **Customer Portal**
- **📦 My Orders**: View, track, and manage orders with real-time status updates
- **💳 My Wallet**: M-Pesa transactions, wallet balance, and payment history
- **🔄 Returns & Refunds**: Easy return process with refund tracking
- **❤️ Wishlist**: Save favorites and share via WhatsApp/social media
- **⚙️ Account Settings**: Personal info, security, and notification preferences
- **💬 Support Center**: WhatsApp, phone, email, and ticket system

### 🛠️ **Admin Panel**
- **📊 Dashboard**: Real-time stats and analytics
- **📦 Order Management**: Update status, track payments, and manage inventory
- **💰 M-Pesa Tracking**: Payment reconciliation and transaction monitoring
- **🔄 Refund Processing**: Manual refund approval and processing
- **📈 Analytics**: Sales reports, payment method analytics, and customer insights
- **⚙️ System Settings**: M-Pesa configuration, notifications, and maintenance

### 🎨 **Modern UI/UX**
- **Responsive Design**: Mobile-first approach with beautiful interface
- **SHEIN-Style Layout**: Grid-based product display with modern aesthetics
- **Fast Loading**: Optimized performance with lazy loading
- **Accessibility**: Screen reader support and keyboard navigation
- **PWA Ready**: Installable web app with offline capabilities

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ 
- npm or yarn
- M-Pesa Developer Account (Safaricom)

### Installation
```bash
# Clone the repository
git clone https://github.com/yourusername/shoplocal.git
cd shoplocal

# Install dependencies
npm install

# Start development server
npm run dev
```

### Environment Setup
Create a `.env` file in the root directory:
```env
# M-Pesa Configuration
MPESA_CONSUMER_KEY=your_consumer_key
MPESA_CONSUMER_SECRET=your_consumer_secret
MPESA_BUSINESS_SHORT_CODE=your_short_code
MPESA_PASSKEY=your_passkey
MPESA_BASE_URL=https://sandbox.safaricom.co.ke

# Database
DATABASE_URL=your_database_url

# Notifications
SMS_API_KEY=your_sms_api_key
WHATSAPP_TOKEN=your_whatsapp_token

# Security
ENCRYPTION_KEY=your_encryption_key
JWT_SECRET=your_jwt_secret
```

## 📱 Pages & Features

### 🏠 **Homepage** (`/`)
- Hero section with call-to-action
- Featured product categories
- Trending products grid
- Benefits section highlighting M-Pesa payments

### 🛒 **Shopping Cart** (`/cart`)
- Item management (add, remove, update quantity)
- Order summary with delivery fees
- Payment method selection
- M-Pesa STK Push integration

### 👤 **Customer Account** (`/account`)
- **My Orders**: Order history and tracking
- **My Wallet**: M-Pesa transactions and balance
- **Returns & Refunds**: Return processing
- **Wishlist**: Saved items
- **Account Settings**: Personal information and preferences
- **Support**: Help center and ticket system

### ✅ **Order Success** (`/order-success`)
- Order confirmation details
- Next steps information
- Contact information
- Receipt download

### 🛠️ **Admin Panel** (`/admin`)
- **Dashboard**: Key metrics and statistics
- **Order Management**: Status updates and tracking
- **M-Pesa Payments**: Transaction monitoring
- **Refunds**: Manual processing
- **Analytics**: Sales and payment reports
- **Settings**: System configuration

## 🔧 Technical Stack

### Frontend
- **Framework**: Astro.js with React components
- **Styling**: Modern CSS with CSS Variables
- **Icons**: Emoji-based icons for better mobile experience
- **Storage**: localStorage for cart and user data
- **Responsive**: Mobile-first design

### Backend Integration
- **Payment Gateway**: Safaricom Daraja API (M-Pesa)
- **SMS Service**: Africa's Talking or similar
- **WhatsApp**: WhatsApp Business API
- **Database**: MySQL/PostgreSQL (schema provided)

### Security
- **Payment Security**: Token-based authentication
- **Data Encryption**: AES-256 encryption for sensitive data
- **IP Validation**: Safaricom IP whitelist validation
- **HTTPS**: SSL/TLS encryption required

## 💳 M-Pesa Integration

### STK Push Flow
1. Customer selects M-Pesa payment
2. Enters phone number (254XXXXXXXX format)
3. STK Push sent to phone
4. Customer enters M-Pesa PIN
5. Payment confirmation received
6. Order status updated automatically

### Supported Features
- **STK Push**: Instant payment prompts
- **Payment Validation**: Real-time verification
- **Refund Processing**: B2C refunds to customer M-Pesa
- **Transaction Tracking**: Complete payment history
- **Reconciliation**: Automatic payment matching

## 📊 Analytics & Reporting

### Available Reports
- **Sales Reports**: Daily, weekly, monthly sales data
- **Payment Analytics**: M-Pesa vs other payment methods
- **Customer Insights**: Order patterns and preferences
- **Refund Reports**: Return and refund statistics
- **Performance Metrics**: Conversion rates and cart abandonment

### Data Export
- **CSV Export**: Order and payment data
- **PDF Reports**: Formatted business reports
- **API Integration**: Real-time data access

## 🔄 Order Management

### Order Statuses
- **Pending**: New orders awaiting confirmation
- **Confirmed**: Payment received and order confirmed
- **Shipped**: Order dispatched for delivery
- **Delivered**: Order successfully delivered
- **Cancelled**: Order cancelled by customer or admin

### Automated Workflows
- **Payment Confirmation**: Automatic status update on payment
- **SMS Notifications**: Order status updates via SMS
- **WhatsApp Updates**: Rich notifications with order details
- **Email Receipts**: Detailed order confirmations

## 🛡️ Security Features

### Payment Security
- **PCI Compliance**: Secure payment processing
- **Token Authentication**: Secure API access
- **Encryption**: All sensitive data encrypted
- **Fraud Detection**: Unusual transaction monitoring

### Data Protection
- **User Privacy**: GDPR-compliant data handling
- **Secure Storage**: Encrypted database storage
- **Access Control**: Role-based permissions
- **Audit Trail**: Complete transaction logging

## 📱 Mobile Experience

### Mobile-First Design
- **Responsive Layout**: Adapts to all screen sizes
- **Touch-Friendly**: Large buttons and easy navigation
- **Fast Loading**: Optimized for mobile networks
- **Offline Support**: PWA capabilities for offline browsing

### Mobile Payment
- **M-Pesa Integration**: Native mobile payment experience
- **One-Click Checkout**: Streamlined mobile checkout
- **SMS Confirmations**: Mobile-friendly notifications
- **App Installation**: PWA installation support

## 🚀 Deployment

### Production Setup
1. **Environment Configuration**: Update environment variables
2. **Database Setup**: Create production database
3. **SSL Certificate**: Configure HTTPS
4. **Domain Setup**: Configure custom domain
5. **M-Pesa Production**: Switch to production endpoints

### Hosting Recommendations
- **Frontend**: Netlify, Vercel, or Cloudflare Pages
- **Backend**: Railway, Heroku, or DigitalOcean
- **Database**: PlanetScale, Supabase, or AWS RDS
- **CDN**: Cloudflare for static assets

## 🔍 Testing

### Test Accounts
- **M-Pesa Sandbox**: Test payments without real money
- **Test Phone Numbers**: 254708374149 (success), 254708374150 (failure)
- **Admin Access**: Demo admin credentials provided

### Testing Checklist
- [ ] M-Pesa STK Push functionality
- [ ] Payment callback handling
- [ ] Order status updates
- [ ] SMS/WhatsApp notifications
- [ ] Admin panel operations
- [ ] Mobile responsiveness
- [ ] Error handling

## 📞 Support

### Getting Help
- **Documentation**: Complete M-Pesa integration guide included
- **GitHub Issues**: Report bugs and feature requests
- **Community**: Join our developer community
- **Professional Support**: Available for enterprise clients

### Resources
- **M-Pesa API Documentation**: [developer.safaricom.co.ke](https://developer.safaricom.co.ke)
- **SMS Gateway**: [africastalking.com](https://africastalking.com)
- **WhatsApp Business**: [business.whatsapp.com](https://business.whatsapp.com)

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

### Development Setup
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

## 🌟 Acknowledgments

- **Safaricom**: For the M-Pesa API
- **Kenya Tech Community**: For feedback and support
- **Open Source Contributors**: For their valuable contributions

---

**Built with ❤️ for Kenya's local sellers**

Transform your local business into a thriving online marketplace with ShopLocal - where technology meets traditional commerce.
