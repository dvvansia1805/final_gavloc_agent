# GRAVLOC Agent

[![License: ISC](https://img.shields.io/badge/License-ISC-blue.svg)](https://opensource.org/licenses/ISC)
[![Node.js](https://img.shields.io/badge/Node.js-18+-green.svg)](https://nodejs.org/)
[![Express](https://img.shields.io/badge/Express-5.x-blue.svg)](https://expressjs.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-14+-blue.svg)](https://www.postgresql.org/)
[![Sequelize](https://img.shields.io/badge/Sequelize-6.x-orange.svg)](https://sequelize.org/)

**GRAVLOC** is the procurement intelligence platform for space-grade components — enabling space companies, startups, research organizations, and engineers to discover, compare, evaluate, and eventually procure mission-critical hardware.

This repository (`final_gavloc_agent`) contains the **agent module** for GRAVLOC's waitlist and supplier onboarding system — the technical foundation forGRAVLOC's B2B marketplace infrastructure.

> **Long-term vision**: Build the "Amazon for space components."
>
> **Current focus**: Technology infrastructure for space-grade hardware sourcing and procurement.

---

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/dvvansia1805/final_gavloc_agent.git
cd final_gavloc_agent

# Install dependencies
npm install

# Create a .env file (see .env.example)
cp .env.example .env

# Run database migrations
npx sequelize-cli db:migrate

# Start the development server
npm run dev
```

The application will be available at `http://localhost:3000`.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Directory Structure](#-directory-structure)
- [Models](#-models)
- [API Endpoints](#-api-endpoints)
- [Database Migrations](#-database-migrations)
- [Environment Variables](#-environment-variables)
- [Development](#-development)
- [Deployment](#-deployment)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🌐 Overview

GRAVLOC is building a **B2B marketplace and procurement intelligence platform** for space components.

### Current State

This `final_gavloc_agent` repository implements:

- **User Waitlist System** — Allows potential buyers to join the waitlist and receive notifications
- **Supplier Waitlist System** — Enables space component suppliers to register and join the platform
- **Email Notification Service** — Automated email notifications using Nodemailer
- **EJS Template Rendering** — Server-side rendering for landing pages
- **Health Check Endpoint** — API health monitoring

### Future State

The agent module will evolve to support:

- Buyer dashboard and RFQ (Request for Quotation) creation
- Supplier dashboard and inventory management
- Component catalog and search
- AI-powered matching and recommendations
- Secure authentication and authorization
- Payment integration for procurement

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         GRAVLOC Agent                               │
├─────────────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐               │
│  │   User       │  │  Supplier    │  │   Email      │               │
│  │   Waitlist   │  │  Waitlist    │  │   Service    │               │
│  └──────────────┘  └──────────────┘  └──────────────┘               │
│         │                 │                  │                       │
│         └─────────────────┴──────────────────┘                       │
│                           │                                          │
│                  ┌────────▼────────┐                                 │
│                  │   Express.js    │                                 │
│                  │   Application   │                                 │
│                  └────────┬────────┘                                 │
│                           │                                          │
│              ┌────────────▼──────────────┐                          │
│              │       Sequelize ORM       │                          │
│              └────────────┬──────────────┘                          │
│                           │                                          │
│                  ┌────────▼────────┐                                 │
│                  │   PostgreSQL    │                                 │
│                  │   Database      │                                 │
│                  └─────────────────┘                                 │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Category         | Technology                                |
|------------------|-------------------------------------------|
| **Runtime**      | Node.js 18+                               |
| **Framework**    | Express.js 5.x                            |
| **Database**     | PostgreSQL 14+                            |
| **ORM**          | Sequelize 6.x                             |
| **Template Engine** | EJS 6.x                              |
| **Email**        | Nodemailer 9.x                            |
| **Validation**   | Joi 18.x                                  |
| **Authentication**| bcrypt, jsonwebtoken                    |
| **Security**     | helmet, cors, cookie-parser               |
| **Logging**      | morgan                                    |
| **DevOps**       | nodemon (development)                     |

---

## 📁 Directory Structure

```
final_gavloc_agent/
├── config/                 # Configuration files
│   ├── config.json         # Sequelize database configuration
│   ├── database.js         # Database connection setup
│   └── env.js              # Environment variable loading
├── migrations/             # Database migrations
│   ├── 20260715173638-create-waitlist.js
│   └── 20260801101509-create-waitlist-supplier.js
├── models/                 # Database models
│   ├── index.js            # Model indexing
│   ├── waitlist.js         # User waitlist model
│   └── supplier_waitlist.js# Supplier waitlist model
├── modules/                # Feature modules
│   ├── notification/       # Email notification service
│   │   ├── mailer.js       # Nodemailer configuration
│   │   ├── notification.service.js
│   │   └── templates/      # Email templates
│   │       ├── supplier-waitlist.template.js
│   │       └── user-waitlist.template.js
│   ├── supplier-waitlist/  # Supplier waitlist module
│   │   ├── supplier-waitlist.controller.js
│   │   ├── supplier-waitlist.routes.js
│   │   ├── supplier-waitlist.service.js
│   │   └── supplier-waitlist.validation.js
│   └── user-waitlist/      # User waitlist module
│       ├── waitlist.controller.js
│       ├── waitlist.routes.js
│       ├── waitlist.service.js
│       └── waitlist.validation.js
├── public/                 # Static assets
│   ├── favicon.ico/        # Favicon assets
│   └── images/             # Application images
├── routes/                 # Route definitions
│   └── health.routes.js    # Health check endpoint
├── views/                  # EJS templates
│   ├── index.ejs           # Landing page
│   ├── supplier.ejs        # Supplier page
│   └── waitlist.ejs        # Waitlist page
├── app.js                  # Express application setup
├── index.js                # Server entry point
├── package.json            # Project dependencies
├── .gitignore              # Git ignore rules
└── README.md               # This file
```

---

## 🗄️ Models

### Waitlist Model

| Field            | Type      | Description                          |
|------------------|-----------|--------------------------------------|
| `id`             | UUID      | Primary key (UUIDv4)                 |
| `email`          | STRING    | User email (unique, validated)       |
| `notification_sent` | BOOLEAN | Notification delivery status       |
| `notified_at`    | DATE      | When notification was sent           |
| `created_at`     | TIMESTAMP | Record creation timestamp            |
| `updated_at`     | TIMESTAMP | Record update timestamp              |

### SupplierWaitlist Model

| Field            | Type      | Description                          |
|------------------|-----------|--------------------------------------|
| `id`             | UUID      | Primary key (UUIDv4)                 |
| `email`          | STRING    | Supplier email (unique, validated)   |
| `notification_sent` | BOOLEAN | Notification delivery status       |
| `notified_at`    | DATE      | When notification was sent           |
| `created_at`     | TIMESTAMP | Record creation timestamp            |
| `updated_at`     | TIMESTAMP | Record update timestamp              |

---

## 🔌 API Endpoints

### Health Check

```http
GET /api/health
```

**Response:**
```json
{
  "status": "ok",
  "timestamp": "2026-09-25T12:00:00.000Z"
}
```

### User Waitlist

```http
POST /api/waitlist/add
Content-Type: application/json

{
  "email": "user@example.com"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Successfully added to waitlist",
  "data": { ...waitlist_entry }
}
```

### Supplier Waitlist

```http
POST /api/supplier-waitlist/add
Content-Type: application/json

{
  "email": "supplier@example.com"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Successfully added to supplier waitlist",
  "data": { ...supplier_waitlist_entry }
}
```

---

## 🗄️ Database Migrations

### Running Migrations

```bash
# Development
npx sequelize-cli db:migrate

# Production
npx sequelize-cli db:migrate --env production
```

### Rollback Migrations

```bash
# Rollback last migration
npx sequelize-cli db:migrate:undo

# Rollback all migrations
npx sequelize-cli db:migrate:undo:all
```

---

## ⚙️ Environment Variables

Create a `.env` file in the root directory:

```env
# Server
PORT=3000
NODE_ENV=development

# Database
DATABASE_URL=postgresql://username:password@localhost:5432/staging_gravloc
DB_HOST=127.0.0.1
DB_PORT=5432
DB_NAME=staging_gravloc
DB_USER=postgres
DB_PASSWORD=user@123

# Email (Nodemailer)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-password
SMTP_FROM=noreply@gravloc.com

# JWT (for future auth)
JWT_SECRET=your-super-secret-jwt-key-change-in-production
JWT_EXPIRE=7d
```

---

## 🛠️ Development

### Local Development

```bash
# Install dependencies
npm install

# Start development server with hot reload
npm run dev
```

The server will start at `http://localhost:3000`.

### Running Tests

```bash
# Currently no test suite configured
npm test
```

### Code Quality

```bash
# Linting (configure eslint when needed)
npm run lint

# Type checking (configure TypeScript when needed)
npm run typecheck
```

---

## 🚢 Deployment

### Docker (Recommended)

```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

RUN npx sequelize-cli db:migrate

EXPOSE 3000

CMD ["node", "index.js"]
```

### Production Environment Variables

For production deployment, ensure these environment variables are set:

```env
NODE_ENV=production
PORT=3000
DATABASE_URL=postgresql://user:pass@prod-db-host:5432/gravloc
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Guidelines

- Write clean, maintainable code
- Follow the existing code style
- Add tests for new features
- Update documentation as needed
- Ensure all checks pass before PR submission

---

## 📄 License

This project is licensed under the **ISC License**.

---

## 🙏 Acknowledgments

- GRAVLOC Team
- Open source contributors
- Space industry professionals

---

## 📞 Contact

For technical inquiries, contact the CTO team at GRAVLOC.

---

**Built with 🚀 by GRAVLOC**
