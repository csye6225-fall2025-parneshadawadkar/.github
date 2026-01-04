# Cloud-Native Web Application Infrastructure

A production-grade, cloud-native web application built with modern DevOps practices, demonstrating enterprise-level architecture, Infrastructure as Code, and event-driven design patterns.

## 🏗️ Architecture Overview

This project implements a complete cloud infrastructure ecosystem comprising:

- **RESTful API Application** with secure user authentication and email verification
- **Auto-scaling Infrastructure** with load balancing and high availability
- **Event-driven Serverless Architecture** for asynchronous processing
- **Comprehensive Monitoring & Logging** with custom metrics and alerts
- **Automated CI/CD Pipelines** for seamless deployments
- **Infrastructure as Code** for reproducible, version-controlled resources

### System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Route 53 (DNS)                          │
│                    parneshadawadkar.me                          │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│              Application Load Balancer (SSL/TLS)                │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Auto Scaling Group                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │   EC2 (AZ1)  │  │   EC2 (AZ2)  │  │   EC2 (AZ3)  │         │
│  │ Custom AMI   │  │ Custom AMI   │  │ Custom AMI   │         │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘         │
└─────────┼──────────────────┼──────────────────┼─────────────────┘
          │                  │                  │
          ├──────────────────┴──────────────────┘
          │
          ▼
┌─────────────────────────────────────────────────────────────────┐
│                  RDS PostgreSQL (Multi-AZ)                      │
│                  Encrypted with KMS                             │
└─────────────────────────────────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────────────────────────────────┐
│                         SNS Topic                               │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Lambda Function                              │
│              (Email Verification Service)                       │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ├─────────────┐
                             ▼             ▼
                      ┌────────────┐  ┌─────────────┐
                      │  SendGrid  │  │  DynamoDB   │
                      │   (Email)  │  │  (Tracking) │
                      └────────────┘  └─────────────┘
```

## 📦 Repository Structure

### [webapp](https://github.com/yourusername/webapp_fork)
**Node.js REST API Application**

The core application providing user management and health check endpoints with secure authentication and email verification.

**Key Features:**
- RESTful API built with Express.js
- PostgreSQL database with Sequelize ORM
- Bcrypt password hashing with salt rounds
- Email verification workflow via SNS
- StatsD metrics collection
- Comprehensive logging with Winston
- Integration and unit testing with Jest

**Tech Stack:** Node.js, Express, PostgreSQL, Sequelize, bcrypt, winston, StatsD

---

### [tf-aws-infra](https://github.com/yourusername/tf-aws-infra_fork)
**Infrastructure as Code - Terraform**

Complete AWS infrastructure provisioning with networking, compute, database, and monitoring resources.

**Key Components:**
- Custom VPC with public/private subnets across 3 AZs
- Application Load Balancer with SSL/TLS termination
- Auto Scaling Groups with launch templates
- RDS PostgreSQL with Multi-AZ deployment
- S3 bucket for application assets
- SNS topics for event publishing
- IAM roles and security groups
- Route 53 DNS configuration
- KMS encryption for all data at rest
- CloudWatch alarms and dashboards

**Tech Stack:** Terraform, AWS (VPC, EC2, RDS, ALB, ASG, S3, SNS, IAM, KMS, CloudWatch, Route 53)

---

### [serverless](https://github.com/yourusername/serverless)
**Lambda Functions - Event Processing**

Serverless email verification service triggered by SNS events with DynamoDB tracking.

**Key Features:**
- Event-driven Lambda function
- SendGrid email integration
- DynamoDB for email tracking and TTL management
- Exponential backoff retry logic
- Comprehensive error handling and logging
- Infrastructure provisioned via Terraform

**Tech Stack:** Node.js, AWS Lambda, DynamoDB, SNS, SendGrid API

## 🚀 Deployment Pipeline

### CI/CD Workflow

1. **Code Push** → Developer commits to repository
2. **GitHub Actions** → Automated workflow triggers
3. **Testing** → Run unit and integration tests
4. **Build** → Create artifacts (AMI/Lambda package)
5. **Deploy** → Update infrastructure and trigger rolling deployment
6. **Verify** → Health checks and smoke tests

### Automated Processes

**webapp Repository:**
- Packer builds custom AMI with application code
- AMI shared across DEV and DEMO accounts
- Auto Scaling Group performs rolling instance refresh
- Zero-downtime deployments

**tf-aws-infra Repository:**
- Terraform plan validation on PR
- Manual approval for infrastructure changes
- State stored in S3 with DynamoDB locking

**serverless Repository:**
- Lambda deployment package creation
- Function code update via Terraform
- Version management and alias updates

## 🔐 Security Features

- **Encryption at Rest:** KMS encryption for RDS, S3, EBS volumes, Lambda environment variables
- **Encryption in Transit:** SSL/TLS certificates for HTTPS, encrypted database connections
- **IAM Security:** Least-privilege policies, role-based access, instance profiles
- **Network Security:** Security groups with minimal ingress rules, private subnets for databases
- **Secrets Management:** AWS Secrets Manager for credentials, environment variables for configuration
- **SSH Key Management:** Separate keys for Packer builds and EC2 access

## 📊 Monitoring & Observability

### CloudWatch Integration
- **Custom Metrics:** API endpoint performance, database query times, user registration counts
- **Alarms:** CPU utilization, database connections, Lambda errors, disk usage
- **Dashboards:** Real-time system health visualization
- **Log Aggregation:** Centralized logging with structured JSON format

### StatsD Metrics
- Counter: API endpoint hit counts
- Timing: Response times and database query duration
- Gauge: Active connections and resource utilization

## 🌐 Environments

### Development (Account: 651636976851)
- Testing and feature development
- Separate VPC and resources
- Lower instance sizes for cost optimization

### Demo/Production (Account: 181975986637)
- Production-ready deployment
- High availability configuration
- Full monitoring and alerting

## 🛠️ Local Development Setup

### Prerequisites
- Node.js 18+ and npm
- PostgreSQL 14+
- AWS CLI configured
- Terraform 1.5+
- Packer 1.9+

### Quick Start

```bash
# Clone repositories
git clone https://github.com/yourusername/webapp_fork.git
git clone https://github.com/yourusername/tf-aws-infra_fork.git
git clone https://github.com/yourusername/serverless.git

# Setup webapp
cd webapp_fork
npm install
cp .env.example .env  # Configure database connection
npm test
npm start

# Setup infrastructure
cd ../tf-aws-infra_fork
terraform init
terraform plan
terraform apply

# Setup serverless
cd ../serverless
npm install
# Deploy via Terraform in tf-aws-infra
```

## 📝 API Documentation

### Health Check
```
GET /healthz
Response: 200 OK (no body)
```

### Create User
```
POST /v1/user
Body: {
  "email": "user@example.com",
  "password": "SecurePass123",
  "first_name": "John",
  "last_name": "Doe"
}
Response: 201 Created
```

### Update User
```
PUT /v1/user/self
Headers: Authorization: Basic <credentials>
Body: {
  "first_name": "Jane",
  "last_name": "Smith",
  "password": "NewSecurePass456"
}
Response: 204 No Content
```

### Get User
```
GET /v1/user/self
Headers: Authorization: Basic <credentials>
Response: 200 OK
```

### Verify Email
```
GET /verify?email=user@example.com&token=abc123def456
Response: 200 OK / 400 Bad Request
```

## 🧪 Testing

### Webapp Tests
```bash
cd webapp_fork
npm test                    # All tests
npm run test:unit          # Unit tests only
npm run test:integration   # Integration tests only
```

### Infrastructure Validation
```bash
cd tf-aws-infra_fork
terraform fmt -check       # Format validation
terraform validate         # Configuration validation
terraform plan            # Preview changes
```

## 📈 Performance Characteristics

- **Response Time:** < 200ms for health checks, < 500ms for API operations
- **Throughput:** Auto-scales to handle traffic spikes
- **Availability:** 99.9% uptime with Multi-AZ deployment
- **Email Delivery:** Asynchronous processing with retry logic
- **Database:** Connection pooling with automatic failover

## 🔄 Continuous Improvement

### Future Enhancements
- [ ] Redis caching layer for improved performance
- [ ] API rate limiting with DynamoDB or Redis
- [ ] Enhanced observability with distributed tracing (X-Ray)
- [ ] Blue-green deployment strategy
- [ ] Automated security scanning in CI/CD
- [ ] Multi-region deployment for global availability

## 📄 License

This project is part of academic coursework at Northeastern University (CSYE 6225 Cloud Computing).

## 👤 Author

**Parnesh Adawadkar**
- Domain: [parneshadawadkar.me](https://parneshadawadkar.me)
- University: Northeastern University
- Program: Master's in Information Systems

## 🙏 Acknowledgments

Built as part of CSYE 6225 Cloud Computing coursework, demonstrating progressive mastery of cloud-native development practices, Infrastructure as Code, and modern DevOps methodologies.

---

**Last Updated:** January 2026
