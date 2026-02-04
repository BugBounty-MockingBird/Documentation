# Getting Started Guide

Welcome to the BugBounty KSP platform! This guide will help you get up and running quickly.

## 📋 Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v18 or higher)
- **npm** or **yarn** package manager
- **Git** for version control
- **Docker** (optional, for containerized deployment)
- **PostgreSQL** (v14 or higher)
- **Redis** (for caching and session management)

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/BugBounty-MockingBird/YourRepository.git
cd YourRepository
```

### 2. Install Dependencies

```bash
npm install
# or
yarn install
```

### 3. Environment Configuration

Create a `.env` file in the root directory:

```env
# Database Configuration
DATABASE_URL=postgresql://user:password@localhost:5432/bugbounty_ksp
REDIS_URL=redis://localhost:6379

# API Configuration
API_PORT=3000
API_BASE_URL=http://localhost:3000

# Discord Configuration
DISCORD_BOT_TOKEN=your_discord_bot_token
DISCORD_CLIENT_ID=your_client_id
DISCORD_CLIENT_SECRET=your_client_secret

# n8n Configuration
N8N_WEBHOOK_URL=http://localhost:5678

# Security
JWT_SECRET=your_secret_key
SESSION_SECRET=your_session_secret

# Environment
NODE_ENV=development
```

### 4. Database Setup

Initialize the database and run migrations:

```bash
npm run db:setup
npm run db:migrate
npm run db:seed
```

### 5. Start the Application

Development mode with hot-reload:

```bash
npm run dev
```

Production mode:

```bash
npm run build
npm start
```

### 6. Verify Installation

Open your browser and navigate to:
- Frontend: `http://localhost:3000`
- API: `http://localhost:3000/api`
- Health Check: `http://localhost:3000/health`

## 📦 Project Structure

```
.
├── src/
│   ├── api/          # API endpoints and controllers
│   ├── components/   # React components
│   ├── services/     # Business logic services
│   ├── models/       # Database models
│   ├── utils/        # Utility functions
│   └── config/       # Configuration files
├── docs/             # Documentation
├── tests/            # Test files
├── public/           # Static assets
└── package.json      # Dependencies
```

## 🔑 First Steps

### 1. Create Your First User

```bash
npm run create-user -- --email admin@example.com --password secure123
```

### 2. Access the Dashboard

Log in with your credentials at `http://localhost:3000/login`

### 3. Explore the Features

- **Dashboard**: Overview of your bounty programs
- **Programs**: Create and manage bug bounty programs
- **Submissions**: Review and triage security reports
- **Integrations**: Connect Discord and n8n workflows
- **Knowledge Base**: Access learning resources

## 🧪 Running Tests

Run the test suite:

```bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run specific test file
npm test path/to/test.spec.js
```

## 🐛 Troubleshooting

### Port Already in Use

If port 3000 is already in use:

```bash
# Change port in .env file
API_PORT=3001
```

### Database Connection Issues

Verify PostgreSQL is running:

```bash
# Check PostgreSQL status
sudo systemctl status postgresql

# Restart PostgreSQL
sudo systemctl restart postgresql
```

### Module Not Found Errors

Clear cache and reinstall:

```bash
rm -rf node_modules package-lock.json
npm install
```

## 📚 Next Steps

- Review the [Architecture & Design Decisions](architecture.md)
- Learn about the [API Reference](api-reference.md)
- Explore [Frontend Components](frontend-components.md)
- Set up [n8n Workflows](n8n-workflows.md)
- Configure [Discord Integration](discord-integration.md)

## 🆘 Getting Help

If you encounter issues:
1. Check the [Troubleshooting](#-troubleshooting) section
2. Search [existing issues](https://github.com/BugBounty-MockingBird/Documentation/issues)
3. Join our Discord community
4. Create a new issue with detailed information

---

[← Back to Home](index.md) | [Next: Architecture →](architecture.md)
