# Golang Microservice E-Commerce Application

A modern, scalable e-commerce application built with Go microservices architecture.

## 🚀 Overview

This project demonstrates a production-ready e-commerce platform using microservices pattern in Go. Each service is independently deployable and communicates via REST APIs.

## 📝 Application Requirements

### E-Commerce Application

Need to build a web application which can facilitate the buy, sale new & used/old products in certain price range. Where seller can post products and advertise it for sale and buyer can search and view product as per choice and buy it online.

Application should provide features to talk to buyer with seller directly without mediator and buy the products by paying the price. Payment system will collect the money of the product and certain % of the final product price will be hold for platform and rest of will be release to seller. The communications of every process will be notify through sms and email notification.

### Key Features

1. **Product Management**
   - Sellers can post and advertise products
   - Support for both new and used/old products
   - Price range filtering
   - Product search and viewing capabilities

2. **User Roles**
   - **Sellers:** Can post products, manage listings, receive payments
   - **Buyers:** Can search products, view details, make purchases

3. **Direct Communication**
   - Buyer-seller direct communication without mediator
   - In-app messaging system

4. **Payment System**
   - Secure online payment processing
   - Platform commission (certain % of product price)
   - Automatic payment distribution to sellers
   - Platform fee retention

5. **Notification System**
   - SMS notifications for all processes
   - Email notifications for all processes
   - Real-time updates on transactions

### Business Flow

1. Seller posts product with details and price
2. Buyer searches and finds products
3. Buyer views product details and communicates with seller
4. Buyer makes purchase through payment gateway
5. Platform retains commission percentage
6. Remaining amount released to seller
7. Both parties receive SMS and email notifications at each step

## 📁 Project Structure

```
golang-microservice/
├── services/
│   ├── user-service/       # User authentication & management
│   ├── product-service/    # Product catalog & inventory
│   ├── order-service/      # Order processing & management
│   └── payment-service/    # Payment processing
├── api-gateway/            # API Gateway & routing
├── docker-compose.yml      # Container orchestration
└── README.md
```

## 🛠️ Tech Stack

- **Language:** Go 1.24+
- **Database:** PostgreSQL
- **Cache:** Redis
- **Message Queue:** RabbitMQ
- **API:** REST
- **Containerization:** Docker

## 🏃 Quick Start

```bash
# Clone the repository
git clone https://github.com/ChanchalS7/golang-mircoservice-ecommerce.git
cd golang-microservice

# Run with Docker Compose
docker-compose up -d

# Access the API
curl http://localhost:8080/health
```

## 📋 Services

| Service | Port | Description |
|---------|------|-------------|
| API Gateway | 8080 | Main entry point |
| User Service | 8081 | User management |
| Product Service | 8082 | Product catalog |
| Order Service | 8083 | Order processing |
| Payment Service | 8084 | Payment handling |

## 🔧 Development

```bash
# Run individual service
cd services/user-service
go run main.go

# Run tests
go test ./...

# Build binary
go build -o bin/user-service
```

## 📚 API Documentation

API documentation available at `http://localhost:8080/docs` after starting the services.



**Built with ❤️ using Go**
