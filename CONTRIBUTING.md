# Contributing to FarmConnect Africa

First off, thank you for considering contributing to FarmConnect Africa! It's people like you who make this project possible and help improve the lives of millions of African farmers.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Coding Standards](#coding-standards)
- [Commit Guidelines](#commit-guidelines)
- [Pull Request Process](#pull-request-process)
- [Community](#community)

## Code of Conduct

This project adheres to a Code of Conduct that all contributors are expected to follow. Please read [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) before contributing.

**In summary:**
- Be respectful and inclusive
- Welcome newcomers
- Focus on what's best for the community
- Show empathy towards others

## How Can I Contribute?

### 🐛 Reporting Bugs

Before creating bug reports, please check existing issues. When creating a bug report, include:

- **Clear title and description**
- **Steps to reproduce** the issue
- **Expected vs. actual behavior**
- **Screenshots** (if applicable)
- **Environment details** (OS, browser, Node version, etc.)

Use the bug report template when creating an issue.

### 💡 Suggesting Features

Feature suggestions are welcome! Please:

- Check if the feature has already been suggested
- Provide a clear use case
- Explain why this feature would benefit farmers or the project
- Consider implementation details if possible

Use the feature request template when creating an issue.

### 🌍 Translations

We need help translating FarmConnect to more African languages!

**Current languages:** English, Swahili, Hausa, Yoruba
**Needed:** Amharic, Oromo, Igbo, Zulu, Somali, French, Arabic, and more

To contribute translations:

1. Check `assets/translations/` for existing translations
2. Copy `en.json` and translate to your language
3. Test with the app locally
4. Submit a PR with your translation

### 📝 Documentation

Help improve our documentation:

- Fix typos or clarify existing docs
- Add examples and tutorials
- Improve API documentation
- Write guides for specific use cases
- Add diagrams and visual aids

### 💻 Code Contributions

All code contributions are welcome, from bug fixes to new features!

## Getting Started

### Prerequisites

- Node.js >= 20.x
- PostgreSQL >= 15
- Redis >= 7
- Docker and Docker Compose (recommended)
- Git

### Fork and Clone

```bash
# Fork the repository on GitHub

# Clone your fork
git clone https://github.com/YOUR_USERNAME/farmconnect-africa.git
cd farmconnect-africa

# Add upstream remote
git remote add upstream https://github.com/farmconnect-africa/farmconnect-africa.git
```

### Install Dependencies

```bash
# Install all dependencies
npm install

# Or use Docker
docker-compose up -d postgres redis rabbitmq
```

### Set Up Environment

```bash
# Copy environment variables
cp .env.example .env

# Edit .env with your local configuration
nano .env
```

### Run Database Migrations

```bash
npm run migration:run
npm run seed
```

### Start Development Server

```bash
# Start all services
npm run dev

# Or individual services
cd services/api && npm run dev
cd apps/web-admin && npm run dev
```

## Development Workflow

### Branch Naming

Use descriptive branch names:

- `feature/add-market-prices` - New features
- `fix/login-error` - Bug fixes
- `docs/api-endpoints` - Documentation
- `refactor/user-service` - Code refactoring
- `test/payment-integration` - Adding tests

### Making Changes

1. **Create a new branch** from `main`:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes** following our coding standards

3. **Write/update tests** for your changes

4. **Run tests locally**:
   ```bash
   npm run test
   npm run lint
   ```

5. **Commit your changes** using conventional commits:
   ```bash
   git add .
   git commit -m "feat: add market price comparison feature"
   ```

6. **Push to your fork**:
   ```bash
   git push origin feature/your-feature-name
   ```

7. **Create a Pull Request** on GitHub

## Coding Standards

### TypeScript/JavaScript

- Use TypeScript for all new code
- Follow ESLint configuration
- Use meaningful variable and function names
- Add JSDoc comments for public APIs
- Avoid `any` types - use proper typing

```typescript
// Good
interface Farmer {
  id: string;
  name: string;
  phoneNumber: string;
  location: GeoLocation;
}

async function getFarmerById(id: string): Promise<Farmer> {
  // Implementation
}

// Bad
function getData(x: any): any {
  // Implementation
}
```

### Code Organization

```
module/
├── dto/              # Data Transfer Objects
├── entities/         # Database entities
├── interfaces/       # TypeScript interfaces
├── services/         # Business logic
├── controllers/      # Route handlers
├── validators/       # Input validation
└── tests/           # Unit and integration tests
```

### Testing

- Write unit tests for business logic
- Write integration tests for APIs
- Aim for >80% code coverage
- Use descriptive test names

```typescript
describe('FarmerService', () => {
  describe('createFarmer', () => {
    it('should create a new farmer with valid data', async () => {
      // Test implementation
    });

    it('should throw error when phone number is invalid', async () => {
      // Test implementation
    });
  });
});
```

### API Design

- Use RESTful conventions
- Version your APIs (`/api/v1/farmers`)
- Use appropriate HTTP status codes
- Return consistent error responses
- Document with Swagger/OpenAPI

```typescript
// Good
POST   /api/v1/farmers              // Create
GET    /api/v1/farmers              // List
GET    /api/v1/farmers/:id          // Get one
PATCH  /api/v1/farmers/:id          // Update
DELETE /api/v1/farmers/:id          // Delete

// Bad
POST   /api/v1/createFarmer
GET    /api/v1/getAllFarmers
```

### Database

- Use migrations for schema changes
- Write seeds for development data
- Index frequently queried fields
- Use transactions for multi-step operations
- Avoid N+1 queries

## Commit Guidelines

We follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks
- `perf`: Performance improvements

### Examples

```bash
feat(farmers): add crop selection during registration

- Added multi-select crop dropdown
- Updated farmer entity to include crops array
- Added migration for crops relationship

Closes #123
```

```bash
fix(api): resolve payment callback timeout issue

The M-Pesa callback was timing out due to long-running
database query. Added background job processing.

Fixes #456
```

## Pull Request Process

### Before Submitting

- [ ] Tests pass locally
- [ ] Code is linted and formatted
- [ ] Documentation is updated
- [ ] Commit messages follow conventions
- [ ] Branch is up to date with `main`

### PR Title

Use the same format as commit messages:
```
feat(scope): description
```

### PR Description

Use the PR template and include:

1. **What** changes were made
2. **Why** these changes are needed
3. **How** to test the changes
4. **Screenshots** (for UI changes)
5. **Related issues** (closes #123)

### Review Process

1. A maintainer will review your PR
2. Address any requested changes
3. Once approved, a maintainer will merge

**Timeline:** We aim to review PRs within 3-5 business days.

## Community

### Communication Channels

- **Slack**: [Join workspace](https://farmconnect-africa.slack.com)
- **Forum**: [Discuss ideas](https://forum.farmconnect.africa)
- **Twitter**: [@FarmConnectAfrica](https://twitter.com/farmconnectafrica)
- **Email**: dev@farmconnect.africa

### Weekly Meetings

- **Developer Sync**: Tuesdays, 3:00 PM EAT (UTC+3)
- **Community Call**: First Saturday of the month, 10:00 AM EAT

Meeting links are posted in Slack #announcements

### Getting Help

- **Technical questions**: Ask in Slack #dev-help
- **Bug reports**: Create a GitHub issue
- **Feature discussions**: Start a forum thread
- **Security issues**: Email security@farmconnect.africa

## Recognition

Contributors are recognized in:

- README.md contributors section
- Annual contributor report
- Social media shoutouts
- Contributor badge on forum

Significant contributors may be invited to join the core team!

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

**Thank you for contributing to FarmConnect Africa! Together, we're transforming African agriculture. 🌾**
