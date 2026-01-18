# Golang Microservice E-Commerce Application

A modern, scalable e-commerce application built with Go microservices architecture.

## 🚀 Overview

This project demonstrates a production-ready e-commerce platform using microservices pattern in Go. Each service is independently deployable and communicates via REST APIs.

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

## 📄 License

This project is for educational purposes.

---

**Built with ❤️ using Go**
