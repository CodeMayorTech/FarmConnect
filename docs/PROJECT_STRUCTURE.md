# FarmConnect Africa - Project Structure

This document provides a detailed overview of the project's directory structure and organization.

## Overview

FarmConnect Africa uses a monorepo structure managed with npm workspaces and Turbo for efficient builds. The project is organized into services, applications, and shared packages.

## Directory Structure

```
farmconnect-africa/
│
├── .github/                        # GitHub specific files
│   ├── workflows/                  # CI/CD pipelines
│   │   ├── api-tests.yml          # API testing workflow
│   │   ├── build.yml              # Build and deployment
│   │   ├── code-quality.yml       # Linting and formatting
│   │   └── security-scan.yml      # Security vulnerability scanning
│   ├── ISSUE_TEMPLATE/            # Issue templates
│   │   ├── bug_report.md
│   │   ├── feature_request.md
│   │   └── translation.md
│   └── PULL_REQUEST_TEMPLATE/     # PR templates
│       └── pull_request_template.md
│
├── apps/                           # Frontend applications
│   ├── web-admin/                 # Admin dashboard (Next.js)
│   │   ├── src/
│   │   │   ├── components/        # React components
│   │   │   ├── pages/            # Next.js pages
│   │   │   ├── hooks/            # Custom React hooks
│   │   │   ├── services/         # API client services
│   │   │   ├── store/            # State management (Zustand)
│   │   │   ├── styles/           # Global styles
│   │   │   └── utils/            # Utility functions
│   │   ├── public/               # Static assets
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   └── next.config.js
│   │
│   ├── mobile-pwa/                # Progressive Web App (React)
│   │   ├── src/
│   │   │   ├── components/
│   │   │   ├── pages/
│   │   │   ├── hooks/
│   │   │   ├── services/
│   │   │   ├── store/
│   │   │   ├── workers/          # Service workers for offline
│   │   │   └── utils/
│   │   ├── public/
│   │   │   ├── manifest.json     # PWA manifest
│   │   │   └── sw.js             # Service worker
│   │   └── package.json
│   │
│   └── mobile-native/             # React Native app (future)
│       ├── android/
│       ├── ios/
│       ├── src/
│       └── package.json
│
├── services/                       # Backend microservices
│   ├── api/                       # Main REST API (NestJS)
│   │   ├── src/
│   │   │   ├── modules/          # Feature modules
│   │   │   │   ├── auth/         # Authentication & authorization
│   │   │   │   │   ├── auth.controller.ts
│   │   │   │   │   ├── auth.service.ts
│   │   │   │   │   ├── auth.module.ts
│   │   │   │   │   ├── strategies/
│   │   │   │   │   ├── guards/
│   │   │   │   │   └── dto/
│   │   │   │   ├── farmers/      # Farmer management
│   │   │   │   │   ├── farmers.controller.ts
│   │   │   │   │   ├── farmers.service.ts
│   │   │   │   │   ├── farmers.module.ts
│   │   │   │   │   ├── entities/
│   │   │   │   │   ├── dto/
│   │   │   │   │   └── validators/
│   │   │   │   ├── crops/        # Crop information
│   │   │   │   ├── weather/      # Weather data & forecasts
│   │   │   │   ├── market/       # Market prices & connections
│   │   │   │   ├── payments/     # Payment processing
│   │   │   │   ├── advisory/     # Agricultural advisories
│   │   │   │   └── analytics/    # Data analytics
│   │   │   ├── common/           # Shared code
│   │   │   │   ├── decorators/
│   │   │   │   ├── filters/
│   │   │   │   ├── guards/
│   │   │   │   ├── interceptors/
│   │   │   │   ├── pipes/
│   │   │   │   └── utils/
│   │   │   ├── config/           # Configuration
│   │   │   │   ├── database.config.ts
│   │   │   │   ├── redis.config.ts
│   │   │   │   └── app.config.ts
│   │   │   ├── database/         # Database setup
│   │   │   │   ├── migrations/
│   │   │   │   ├── seeds/
│   │   │   │   └── data-source.ts
│   │   │   ├── app.module.ts
│   │   │   └── main.ts
│   │   ├── test/                 # Tests
│   │   │   ├── unit/
│   │   │   ├── integration/
│   │   │   └── e2e/
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   ├── nest-cli.json
│   │   └── Dockerfile
│   │
│   ├── ml/                        # Machine Learning Service (Python/FastAPI)
│   │   ├── src/
│   │   │   ├── api/              # API endpoints
│   │   │   ├── models/           # ML model definitions
│   │   │   │   ├── disease_detection/
│   │   │   │   ├── yield_prediction/
│   │   │   │   └── price_forecast/
│   │   │   ├── preprocessing/    # Data preprocessing
│   │   │   ├── training/         # Model training scripts
│   │   │   └── utils/
│   │   ├── models/               # Trained model files
│   │   ├── data/                 # Training data
│   │   ├── notebooks/            # Jupyter notebooks
│   │   ├── requirements.txt
│   │   ├── app.py
│   │   └── Dockerfile
│   │
│   ├── ussd-gateway/             # USSD Service (Node.js)
│   │   ├── src/
│   │   │   ├── controllers/
│   │   │   ├── services/
│   │   │   ├── menus/            # USSD menu definitions
│   │   │   ├── sessions/         # Session management
│   │   │   └── utils/
│   │   ├── package.json
│   │   └── Dockerfile
│   │
│   ├── sms-gateway/              # SMS Service (Node.js)
│   │   ├── src/
│   │   │   ├── controllers/
│   │   │   ├── services/
│   │   │   ├── templates/        # SMS templates
│   │   │   └── utils/
│   │   ├── package.json
│   │   └── Dockerfile
│   │
│   └── notification-service/     # Push & Email Notifications
│       ├── src/
│       │   ├── controllers/
│       │   ├── services/
│       │   ├── templates/        # Email templates
│       │   └── workers/          # Background jobs
│       ├── package.json
│       └── Dockerfile
│
├── packages/                       # Shared packages
│   ├── ui-components/             # Shared React components
│   │   ├── src/
│   │   │   ├── Button/
│   │   │   ├── Input/
│   │   │   ├── Card/
│   │   │   └── index.ts
│   │   └── package.json
│   │
│   ├── types/                     # Shared TypeScript types
│   │   ├── src/
│   │   │   ├── farmer.types.ts
│   │   │   ├── market.types.ts
│   │   │   └── index.ts
│   │   └── package.json
│   │
│   ├── utils/                     # Shared utilities
│   │   ├── src/
│   │   │   ├── validation.ts
│   │   │   ├── formatting.ts
│   │   │   └── index.ts
│   │   └── package.json
│   │
│   └── constants/                 # Shared constants
│       ├── src/
│       │   ├── crops.ts
│       │   ├── languages.ts
│       │   └── index.ts
│       └── package.json
│
├── database/                       # Database files
│   ├── migrations/                # SQL migrations
│   │   ├── 001_create_farmers_table.sql
│   │   ├── 002_create_crops_table.sql
│   │   └── ...
│   ├── seeds/                     # Seed data
│   │   ├── 01_crops.sql
│   │   ├── 02_demo_farmers.sql
│   │   └── ...
│   └── schemas/                   # Database schemas
│       ├── farmer.schema.sql
│       └── ...
│
├── infrastructure/                 # Infrastructure as Code
│   ├── terraform/                 # Terraform configs
│   │   ├── modules/
│   │   │   ├── vpc/
│   │   │   ├── eks/
│   │   │   └── rds/
│   │   ├── environments/
│   │   │   ├── dev/
│   │   │   ├── staging/
│   │   │   └── production/
│   │   └── main.tf
│   │
│   ├── kubernetes/                # K8s manifests
│   │   ├── base/                 # Base configs
│   │   │   ├── deployments/
│   │   │   ├── services/
│   │   │   ├── configmaps/
│   │   │   └── secrets/
│   │   └── overlays/             # Environment overlays
│   │       ├── development/
│   │       ├── staging/
│   │       └── production/
│   │
│   └── docker/                    # Docker configs
│       └── compose/
│           ├── docker-compose.yml
│           ├── docker-compose.dev.yml
│           └── docker-compose.prod.yml
│
├── docs/                           # Documentation
│   ├── api/                       # API documentation
│   │   ├── README.md
│   │   ├── authentication.md
│   │   ├── farmers.md
│   │   └── market.md
│   ├── architecture/              # Architecture docs
│   │   ├── system-design.md
│   │   ├── data-model.md
│   │   └── security.md
│   ├── guides/                    # User guides
│   │   ├── getting-started.md
│   │   ├── deployment.md
│   │   ├── localization.md
│   │   └── contributing.md
│   └── diagrams/                  # Architecture diagrams
│       ├── system-architecture.png
│       ├── data-flow.png
│       └── deployment.png
│
├── scripts/                        # Utility scripts
│   ├── deployment/                # Deployment scripts
│   │   ├── deploy-dev.sh
│   │   ├── deploy-staging.sh
│   │   └── deploy-production.sh
│   ├── database/                  # Database scripts
│   │   ├── backup.sh
│   │   ├── restore.sh
│   │   └── migrate.sh
│   ├── seeds/                     # Data seeding
│   │   └── import-crops.ts
│   └── utils/                     # Helper scripts
│       ├── generate-types.ts
│       └── check-translations.ts
│
├── tests/                          # Cross-service tests
│   ├── e2e/                       # End-to-end tests
│   │   ├── farmer-journey.test.ts
│   │   └── market-transaction.test.ts
│   ├── integration/               # Integration tests
│   │   └── payment-flow.test.ts
│   └── load/                      # Load testing
│       ├── locust/
│       └── k6/
│
├── assets/                         # Static assets
│   ├── images/
│   │   ├── logo.png
│   │   └── onboarding/
│   ├── icons/
│   └── translations/              # i18n files
│       ├── en.json
│       ├── sw.json                # Swahili
│       ├── ha.json                # Hausa
│       └── yo.json                # Yoruba
│
├── config/                         # Configuration files
│   ├── development/
│   ├── staging/
│   └── production/
│
├── .github/                        # GitHub configs
├── .gitignore
├── .env.example
├── .eslintrc.js
├── .prettierrc
├── docker-compose.yml
├── package.json                    # Root package.json
├── turbo.json                      # Turbo config
├── tsconfig.json                   # Root TypeScript config
├── README.md
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── LICENSE
└── CHANGELOG.md
```

## Key Concepts

### Monorepo Structure

We use a monorepo to:
- Share code between services easily
- Maintain consistent dependencies
- Simplify development workflow
- Enable atomic changes across services

### Service Architecture

Each service is:
- **Independent**: Can be deployed separately
- **Scalable**: Can scale horizontally
- **Testable**: Has its own test suite
- **Documented**: Has API documentation

### Shared Packages

Shared packages contain:
- UI components used across frontends
- TypeScript types shared between services
- Utility functions used everywhere
- Constants and configurations

### Infrastructure as Code

All infrastructure is:
- Version controlled
- Environment-specific
- Reproducible
- Documented

## Development Flow

1. **Local Development**: Use Docker Compose
2. **Testing**: Run tests in each service
3. **Building**: Turbo builds all packages
4. **Deployment**: CI/CD pipelines deploy to cloud

## Getting Started

See [README.md](../README.md) for setup instructions.

## Questions?

- Check [docs/guides/](guides/)
- Ask in Slack #dev-help
- Create a GitHub issue
