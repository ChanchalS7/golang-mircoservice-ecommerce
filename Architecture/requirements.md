# E-Commerce Application Requirements

## Application Overview

Need to build a web application which can facilitate the buy, sale new & used/old products in certain price range. Where seller can post products and advertise it for sale and buyer can search and view ## Future Enhancements

1. Review and rating system
2. Wishlist functionality
3. Advanced search with AI
4. Price negotiation feature
5. Auction functionality
6. Mobile applications
7. Social media integration
8. Analytics dashboard
9. Multi-currency support
10. International shipping integration

## API Endpoints Design

### User Service APIs
- `POST /api/v1/users/register` - Register new user
- `POST /api/v1/users/login` - User login
- `GET /api/v1/users/profile` - Get user profile
- `PUT /api/v1/users/profile` - Update user profile
- `POST /api/v1/users/logout` - User logout

### Product Service APIs
- `POST /api/v1/products` - Create new product (Seller only)
- `GET /api/v1/products` - List all products with filters
- `GET /api/v1/products/:id` - Get product details
- `PUT /api/v1/products/:id` - Update product (Seller only)
- `DELETE /api/v1/products/:id` - Delete product (Seller only)
- `GET /api/v1/products/search` - Search products
- `GET /api/v1/products/seller/:sellerId` - Get seller's products

### Order Service APIs
- `POST /api/v1/orders` - Create new order
- `GET /api/v1/orders` - Get user's orders
- `GET /api/v1/orders/:id` - Get order details
- `PUT /api/v1/orders/:id/status` - Update order status

### Payment Service APIs
- `POST /api/v1/payments/initiate` - Initiate payment
- `POST /api/v1/payments/webhook` - Payment gateway webhook
- `GET /api/v1/payments/:orderId` - Get payment details
- `POST /api/v1/payments/refund` - Process refund

### Messaging Service APIs
- `POST /api/v1/messages` - Send message
- `GET /api/v1/messages/conversations` - Get all conversations
- `GET /api/v1/messages/:conversationId` - Get conversation messages
- `PUT /api/v1/messages/:id/read` - Mark message as read

### Notification Service APIs
- `POST /api/v1/notifications/send` - Send notification (internal)
- `GET /api/v1/notifications` - Get user notifications
- `PUT /api/v1/notifications/:id/read` - Mark notification as read
- `GET /api/v1/notifications/preferences` - Get notification preferences
- `PUT /api/v1/notifications/preferences` - Update preferences

## Database Schema

### Users Table
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20) UNIQUE NOT NULL,
    role VARCHAR(20) NOT NULL CHECK (role IN ('buyer', 'seller')),
    password_hash VARCHAR(255) NOT NULL,
    is_verified BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Products Table
```sql
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    seller_id UUID NOT NULL REFERENCES users(id),
    title VARCHAR(255) NOT NULL,
    description TEXT,
    condition VARCHAR(20) CHECK (condition IN ('new', 'used')),
    price DECIMAL(10, 2) NOT NULL,
    category VARCHAR(100),
    images JSONB,
    status VARCHAR(20) DEFAULT 'active' CHECK (status IN ('active', 'sold', 'inactive')),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Orders Table
```sql
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    buyer_id UUID NOT NULL REFERENCES users(id),
    seller_id UUID NOT NULL REFERENCES users(id),
    product_id UUID NOT NULL REFERENCES products(id),
    total_amount DECIMAL(10, 2) NOT NULL,
    platform_commission DECIMAL(10, 2) NOT NULL,
    seller_amount DECIMAL(10, 2) NOT NULL,
    commission_percentage DECIMAL(5, 2) NOT NULL,
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN ('pending', 'paid', 'completed', 'cancelled', 'refunded')),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Transactions Table
```sql
CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    order_id UUID NOT NULL REFERENCES orders(id),
    gateway_transaction_id VARCHAR(255),
    amount DECIMAL(10, 2) NOT NULL,
    status VARCHAR(20) CHECK (status IN ('pending', 'success', 'failed', 'refunded')),
    payment_method VARCHAR(50),
    gateway_response JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Messages Table
```sql
CREATE TABLE messages (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    sender_id UUID NOT NULL REFERENCES users(id),
    receiver_id UUID NOT NULL REFERENCES users(id),
    product_id UUID REFERENCES products(id),
    content TEXT NOT NULL,
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Notifications Table
```sql
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID NOT NULL REFERENCES users(id),
    type VARCHAR(20) CHECK (type IN ('sms', 'email', 'push')),
    subject VARCHAR(255),
    content TEXT NOT NULL,
    status VARCHAR(20) CHECK (status IN ('pending', 'sent', 'failed')),
    sent_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Environment Configuration

### Required Environment Variables

```env
# Application
APP_ENV=development
APP_PORT=8080
APP_NAME=ecommerce-platform

# Database
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=your_password
DB_NAME=ecommerce_db

# Redis Cache
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=

# JWT
JWT_SECRET=your_jwt_secret_key
JWT_EXPIRY=24h

# Payment Gateway (Stripe/Razorpay)
PAYMENT_GATEWAY=stripe
STRIPE_SECRET_KEY=sk_test_xxxxx
STRIPE_WEBHOOK_SECRET=whsec_xxxxx
PLATFORM_COMMISSION_PERCENTAGE=5.0

# SMS Gateway (Twilio)
TWILIO_ACCOUNT_SID=ACxxxxx
TWILIO_AUTH_TOKEN=xxxxx
TWILIO_PHONE_NUMBER=+1234567890

# Email Service (SendGrid)
SENDGRID_API_KEY=SG.xxxxx
SENDGRID_FROM_EMAIL=noreply@ecommerce.com
SENDGRID_FROM_NAME=E-Commerce Platform

# Message Queue (RabbitMQ)
RABBITMQ_URL=amqp://guest:guest@localhost:5672/

# AWS S3 (for image storage)
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=xxxxx
AWS_SECRET_ACCESS_KEY=xxxxx
S3_BUCKET_NAME=ecommerce-products

# API Gateway
GATEWAY_PORT=8080
USER_SERVICE_URL=http://localhost:8081
PRODUCT_SERVICE_URL=http://localhost:8082
ORDER_SERVICE_URL=http://localhost:8083
PAYMENT_SERVICE_URL=http://localhost:8084
MESSAGING_SERVICE_URL=http://localhost:8085
NOTIFICATION_SERVICE_URL=http://localhost:8086
```

## Deployment Strategy

### Development Environment
- Local development with Docker Compose
- Hot reload for rapid development
- Mock payment gateway for testing

### Staging Environment
- Kubernetes cluster
- Test payment gateway
- Real SMS/Email services with test numbers

### Production Environment
- Kubernetes with auto-scaling
- High availability setup
- Real payment gateway
- CDN for static assets
- Load balancer
- Monitoring and alerting

## Testing Strategy

### Unit Testing
- Test individual functions and methods
- Mock external dependencies
- Aim for 80%+ code coverage

### Integration Testing
- Test service interactions
- Test database operations
- Test API endpoints

### End-to-End Testing
- Test complete user flows
- Test payment processing
- Test notification delivery

### Performance Testing
- Load testing with expected traffic
- Stress testing for peak loads
- Database query optimization

## Monitoring and Observability

### Metrics to Track
- Request rate per service
- Response time
- Error rate
- Database connection pool
- Payment success rate
- Notification delivery rate
- Active users
- Product views
- Conversion rate

### Logging
- Structured logging (JSON format)
- Centralized log aggregation (ELK/CloudWatch)
- Log levels (DEBUG, INFO, WARN, ERROR)
- Request/Response logging

### Alerting
- Service down alerts
- High error rate alerts
- Payment failure alerts
- Database connection issues
- High latency alerts

## Compliance and Legal

### Data Protection
- GDPR compliance for EU users
- Data encryption
- Right to erasure
- Data portability

### Payment Compliance
- PCI-DSS compliance
- Secure payment data handling
- No storage of sensitive card data

### Terms and Conditions
- Platform usage terms
- Commission policy
- Refund policy
- Dispute resolution

## Architecture Diagram

![E-Commerce Application Architecture](<Screenshot 2026-01-19 at 11.25.43 PM.png>)

---

## Document Version
- **Version:** 1.0
- **Last Updated:** January 19, 2026
- **Author:** Development Team
- **Status:** Draftr choice and buy it online.

Application should provide features to talk to buyer with seller directly without mediator and buy the products by paying the price. Payment system will collect the money of the product and certain % of the final product price will be hold for platform and rest of will be release to seller. The communications of every process will be notify through sms and email notification.

## Core Requirements

### 1. Product Management
- Support for new and used/old products
- Price range filtering and search
- Product listing and advertisement by sellers
- Product search and viewing by buyers

### 2. User Roles

#### Sellers
- Post products with details
- Advertise products for sale
- Manage product listings
- Receive payments (minus platform commission)
- Communicate with buyers

#### Buyers
- Search products by criteria
- View product details
- Filter by price range
- Purchase products online
- Communicate with sellers

### 3. Communication System
- Direct buyer-to-seller communication
- No mediator required
- In-app messaging/chat functionality
- Real-time communication support

### 4. Payment System

#### Payment Flow
1. Buyer pays full product price
2. Payment gateway collects the money
3. Platform retains commission (certain % of final price)
4. Remaining amount released to seller

#### Features
- Secure payment processing
- Automatic commission calculation
- Payment split (platform commission vs seller payment)
- Transaction tracking
- Escrow-like functionality

### 5. Notification System

#### Channels
- SMS notifications
- Email notifications

#### Triggers
- Product posted by seller
- Product search/inquiry by buyer
- Message received
- Purchase initiated
- Payment processed
- Payment split completed
- Amount released to seller
- Order completed
- Any other process updates

## Technical Requirements

### Functional Requirements
1. User registration and authentication (Buyer/Seller roles)
2. Product CRUD operations
3. Search and filter functionality
4. Direct messaging system
5. Payment gateway integration
6. Commission calculation engine
7. Payment distribution system
8. SMS gateway integration
9. Email service integration
10. Notification management system

### Non-Functional Requirements
1. Scalability to handle multiple users
2. Secure payment processing
3. Real-time communication
4. High availability
5. Data consistency
6. Transaction integrity
7. Fast search and retrieval
8. Mobile-responsive design

## Microservices Architecture

### Proposed Services

1. **User Service**
   - User registration/authentication
   - Profile management
   - Role management (Buyer/Seller)

2. **Product Service**
   - Product CRUD operations
   - Product search and filtering
   - Inventory management
   - Price range filtering

3. **Messaging Service**
   - Direct buyer-seller communication
   - Chat history
   - Real-time messaging

4. **Order Service**
   - Order creation
   - Order tracking
   - Order history

5. **Payment Service**
   - Payment processing
   - Commission calculation
   - Payment distribution
   - Transaction management

6. **Notification Service**
   - SMS notifications
   - Email notifications
   - Notification templates
   - Notification queue management

7. **API Gateway**
   - Single entry point
   - Request routing
   - Authentication/Authorization
   - Rate limiting

## Data Models

### User
- ID
- Name
- Email
- Phone
- Role (Buyer/Seller)
- Password (hashed)
- Created At
- Updated At

### Product
- ID
- Seller ID
- Title
- Description
- Condition (New/Used)
- Price
- Category
- Images
- Status (Active/Sold/Inactive)
- Created At
- Updated At

### Order
- ID
- Buyer ID
- Seller ID
- Product ID
- Total Amount
- Platform Commission
- Seller Amount
- Status
- Created At
- Updated At

### Transaction
- ID
- Order ID
- Payment Gateway Transaction ID
- Amount
- Status
- Payment Method
- Created At
- Updated At

### Message
- ID
- Sender ID
- Receiver ID
- Product ID (optional)
- Message Content
- Read Status
- Created At

### Notification
- ID
- User ID
- Type (SMS/Email)
- Content
- Status
- Sent At
- Created At

## Integration Points

1. **Payment Gateway**
   - Stripe/PayPal/Razorpay
   - Webhook for payment confirmations

2. **SMS Gateway**
   - Twilio/AWS SNS
   - SMS templates

3. **Email Service**
   - SendGrid/AWS SES
   - Email templates

## Security Considerations

1. User authentication and authorization
2. Secure payment processing (PCI compliance)
3. Data encryption at rest and in transit
4. API security (JWT tokens)
5. Input validation and sanitization
6. Rate limiting and DDoS protection
7. Secure messaging (end-to-end encryption optional)

## Workflow

### Product Listing Flow
1. Seller registers/logs in
2. Seller creates product listing
3. System validates and saves product
4. Notification sent to seller (confirmation)

### Purchase Flow
1. Buyer searches for products
2. Buyer views product details
3. Buyer optionally messages seller
4. Buyer initiates purchase
5. Buyer redirected to payment gateway
6. Payment processed
7. Platform commission calculated and held
8. Remaining amount marked for seller
9. Notifications sent (SMS + Email) to both parties
10. Order marked as completed
11. Seller amount released

### Communication Flow
1. Buyer/Seller initiates message
2. Message saved to database
3. Real-time notification to recipient
4. SMS/Email notification sent
5. Recipient views message
6. Conversation continues

## Success Metrics

1. Number of products listed
2. Number of successful transactions
3. Platform commission revenue
4. User engagement (messages, searches)
5. Notification delivery rate
6. Payment success rate
7. Average response time
8. System uptime

## Future Enhancements

1. Review and rating system
2. Wishlist functionality
3. Advanced search with AI
4. Price negotiation feature
5. Auction functionality
6. Mobile applications
7. Social media integration
8. Analytics dashboard
9. Multi-currency support
10. International shipping integration
![alt text](<Screenshot 2026-01-19 at 11.25.43 PM.png>)![alt text](<Screenshot 2026-01-19 at 11.25.43 PM-1.png>)