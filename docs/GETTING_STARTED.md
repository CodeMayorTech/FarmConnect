# Getting Started with FarmConnect Africa

This guide will help you set up FarmConnect Africa on your local machine for development.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v20.x or higher) - [Download](https://nodejs.org/)
- **npm** (v10.x or higher) - Comes with Node.js
- **PostgreSQL** (v15 or higher) - [Download](https://www.postgresql.org/download/)
- **Redis** (v7 or higher) - [Download](https://redis.io/download)
- **Docker** (optional but recommended) - [Download](https://www.docker.com/products/docker-desktop)
- **Git** - [Download](https://git-scm.com/downloads)

## Quick Start with Docker (Recommended)

The fastest way to get started is using Docker Compose:

```bash
# 1. Clone the repository
git clone https://github.com/your-org/farmconnect-africa.git
cd farmconnect-africa

# 2. Copy environment variables
cp .env.example .env

# 3. Start all services
docker-compose up -d

# 4. Run database migrations
docker-compose exec api npm run migration:run

# 5. Seed the database
docker-compose exec api npm run seed
```

That's it! The application is now running:

- **API**: http://localhost:3000
- **API Docs**: http://localhost:3000/docs
- **Admin Dashboard**: http://localhost:3100
- **Mobile PWA**: http://localhost:3200
- **RabbitMQ Management**: http://localhost:15672 (user: farmconnect, pass: farmconnect_dev_password)
- **MinIO Console**: http://localhost:9001 (user: farmconnect, pass: farmconnect_dev_password)

## Manual Setup (Without Docker)

If you prefer to run services manually:

### Step 1: Install Dependencies

```bash
# Clone the repository
git clone https://github.com/your-org/farmconnect-africa.git
cd farmconnect-africa

# Install dependencies for all packages
npm install
```

### Step 2: Set Up PostgreSQL

```bash
# Create database
psql -U postgres
CREATE DATABASE farmconnect;
CREATE USER farmconnect WITH PASSWORD 'your_password';
GRANT ALL PRIVILEGES ON DATABASE farmconnect TO farmconnect;

# Enable PostGIS extension
\c farmconnect
CREATE EXTENSION postgis;
\q
```

### Step 3: Set Up Redis

```bash
# Start Redis server
redis-server

# Verify it's running
redis-cli ping
# Should return: PONG
```

### Step 4: Configure Environment

```bash
# Copy environment template
cp .env.example .env

# Edit .env with your database credentials
nano .env
```

Update these values in `.env`:
```env
DATABASE_URL=postgresql://farmconnect:your_password@localhost:5432/farmconnect
REDIS_URL=redis://localhost:6379
```

### Step 5: Run Migrations

```bash
# Run database migrations
npm run migration:run

# Seed initial data
npm run seed
```

### Step 6: Start Development Servers

Open 3 terminal windows:

**Terminal 1 - API Service:**
```bash
cd services/api
npm run dev
# API will start on http://localhost:3000
```

**Terminal 2 - Web Admin:**
```bash
cd apps/web-admin
npm run dev
# Dashboard will start on http://localhost:3100
```

**Terminal 3 - Mobile PWA:**
```bash
cd apps/mobile-pwa
npm start
# PWA will start on http://localhost:3200
```

## Verify Installation

### Check API Health

```bash
curl http://localhost:3000/health
```

Expected response:
```json
{
  "status": "ok",
  "timestamp": "2026-02-13T12:00:00.000Z",
  "database": "connected",
  "redis": "connected"
}
```

### Access API Documentation

Open your browser and go to: http://localhost:3000/docs

You should see the Swagger API documentation.

### Test USSD Simulator (Optional)

```bash
# Start USSD gateway
cd services/ussd-gateway
npm run dev

# Test USSD endpoint
curl -X POST http://localhost:3001/ussd \
  -H "Content-Type: application/json" \
  -d '{
    "sessionId": "test123",
    "serviceCode": "*384*96#",
    "phoneNumber": "+254712345678",
    "text": ""
  }'
```

## Create Your First Farmer Account

### Using API

```bash
# Register a farmer
curl -X POST http://localhost:3000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "phoneNumber": "+254712345678",
    "name": "John Doe",
    "location": {
      "latitude": -1.286389,
      "longitude": 36.817223
    },
    "language": "en"
  }'
```

### Using Admin Dashboard

1. Open http://localhost:3100
2. Login with default admin credentials:
   - Email: admin@farmconnect.africa
   - Password: admin123 (change immediately!)
3. Navigate to "Farmers" → "Add New Farmer"
4. Fill in the form and submit

## Common Issues & Solutions

### Issue: Port Already in Use

**Error**: `Port 3000 is already in use`

**Solution**:
```bash
# Find process using the port
lsof -i :3000

# Kill the process
kill -9 <PID>

# Or use a different port
PORT=3001 npm run dev
```

### Issue: Database Connection Failed

**Error**: `ECONNREFUSED` or `Connection refused`

**Solutions**:
1. Check if PostgreSQL is running:
   ```bash
   pg_isready
   ```

2. Verify credentials in `.env`:
   ```env
   DATABASE_URL=postgresql://user:password@localhost:5432/database
   ```

3. Check PostgreSQL logs:
   ```bash
   tail -f /usr/local/var/postgres/server.log
   ```

### Issue: Redis Connection Failed

**Solution**:
```bash
# Check if Redis is running
redis-cli ping

# Start Redis if not running
redis-server

# Or with Docker
docker run -d -p 6379:6379 redis:7-alpine
```

### Issue: Node Modules Not Found

**Solution**:
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm cache clean --force
npm install
```

### Issue: Permission Denied on Linux/Mac

**Solution**:
```bash
# Fix npm permissions
sudo chown -R $USER:$(id -gn $USER) ~/.npm
sudo chown -R $USER:$(id -gn $USER) ~/.config
```

## Development Workflow

### Making Changes

1. Create a new branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. Make your changes

3. Run tests:
   ```bash
   npm run test
   npm run lint
   ```

4. Commit changes:
   ```bash
   git add .
   git commit -m "feat: add your feature description"
   ```

5. Push to your fork:
   ```bash
   git push origin feature/your-feature-name
   ```

6. Create a Pull Request on GitHub

### Running Tests

```bash
# Run all tests
npm run test

# Run tests for specific service
cd services/api
npm run test

# Run tests in watch mode
npm run test:watch

# Run e2e tests
npm run test:e2e

# Check test coverage
npm run test:cov
```

### Code Quality

```bash
# Lint all code
npm run lint

# Fix lint issues
npm run lint:fix

# Format code
npm run format

# Type check
npm run type-check
```

### Database Management

```bash
# Create a new migration
npm run migration:create --name=add-new-field

# Run migrations
npm run migration:run

# Revert last migration
npm run migration:revert

# Seed database
npm run seed

# Reset database (DANGER: Deletes all data)
npm run db:reset
```

## IDE Setup

### VS Code (Recommended)

Install recommended extensions:

1. **ESLint** - For linting
2. **Prettier** - For formatting
3. **TypeScript Vue Plugin** - For TypeScript support
4. **Docker** - For Docker support
5. **GitLens** - For Git integration

Our repository includes VS Code settings in `.vscode/settings.json`.

### Other IDEs

Configuration for WebStorm, Sublime Text, and other IDEs can be found in the [IDE Setup Guide](../docs/guides/ide-setup.md).

## Useful Commands

```bash
# Start all services
npm run dev

# Build all packages
npm run build

# Clean all build artifacts
npm run clean

# Update all dependencies
npm run update-deps

# Check for outdated dependencies
npm run outdated

# Generate TypeScript types
npm run generate-types

# View logs
docker-compose logs -f api
docker-compose logs -f postgres

# Access database
psql postgresql://farmconnect:password@localhost:5432/farmconnect

# Access Redis CLI
redis-cli
```

## Next Steps

Now that you have FarmConnect Africa running locally:

1. **Explore the API**: Check out http://localhost:3000/docs
2. **Read the Architecture**: See [ARCHITECTURE.md](../docs/ARCHITECTURE.md)
3. **Review the Code**: Start with `services/api/src/main.ts`
4. **Join the Community**: Slack at https://farmconnect-africa.slack.com
5. **Pick an Issue**: Check [Good First Issues](https://github.com/your-org/farmconnect-africa/labels/good%20first%20issue)

## Getting Help

- **Documentation**: [docs/](../docs/)
- **FAQ**: [docs/FAQ.md](../docs/FAQ.md)
- **Slack**: #dev-help channel
- **Email**: support@farmconnect.africa
- **GitHub Issues**: [Create an issue](https://github.com/your-org/farmconnect-africa/issues/new)

## Additional Resources

- [Contributing Guide](../CONTRIBUTING.md)
- [API Documentation](../docs/api/README.md)
- [Database Schema](../docs/architecture/data-model.md)
- [Security Best Practices](../docs/security.md)
- [Deployment Guide](../docs/deployment.md)

---

**Welcome to the FarmConnect Africa community! Happy coding! 🌾**
