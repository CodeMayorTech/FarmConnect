# FarmConnect Africa - Technical Architecture

## System Overview

FarmConnect Africa is built as a cloud-native, microservices-based platform designed for high availability, scalability, and offline-first operation. The architecture prioritizes accessibility for users with limited connectivity and basic mobile devices.

## Architecture Principles

1. **Offline-First**: All critical features work without internet
2. **Mobile-First**: Optimized for low-end devices and slow networks
3. **Multilingual**: Support for 20+ African languages
4. **Accessibility**: USSD/SMS for feature phones, PWA for smartphones
5. **Scalability**: Microservices that can scale independently
6. **Security**: End-to-end encryption, PCI DSS compliance
7. **Data Privacy**: GDPR and local data protection compliance

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Client Layer                              │
├──────────────┬──────────────┬──────────────┬────────────────────┤
│  Feature     │   WhatsApp   │  Mobile PWA   │   Web Admin        │
│  Phones      │   Business   │  (Android/iOS)│   Dashboard        │
│  (USSD/SMS)  │              │               │                    │
└──────────────┴──────────────┴──────────────┴────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     API Gateway Layer                            │
│  - Authentication & Authorization                                │
│  - Rate Limiting                                                 │
│  - Request Routing                                               │
│  - Load Balancing                                                │
└─────────────────────────────────────────────────────────────────┘
                              │
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
│  Core API        │ │  USSD Gateway    │ │  SMS Gateway     │
│  Service         │ │  Service         │ │  Service         │
│  (NestJS)        │ │  (Node.js)       │ │  (Node.js)       │
└──────────────────┘ └──────────────────┘ └──────────────────┘
         │                    │                    │
         └────────────────────┼────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Business Logic Layer                          │
├──────────────┬──────────────┬──────────────┬────────────────────┤
│  Farmer      │  Market      │  Agricultural│  Payment           │
│  Management  │  Connect     │  Intelligence│  Processing        │
└──────────────┴──────────────┴──────────────┴────────────────────┘
                              │
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
│  PostgreSQL      │ │  Redis Cache     │ │  RabbitMQ        │
│  (Primary DB)    │ │  (Sessions)      │ │  (Job Queue)     │
└──────────────────┘ └──────────────────┘ └──────────────────┘
         │
         └────────────────────┐
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Integration Layer                             │
├──────────────┬──────────────┬──────────────┬────────────────────┤
│  Weather     │  Payment     │  ML Service  │  Notification      │
│  APIs        │  Gateways    │  (Python)    │  Service           │
└──────────────┴──────────────┴──────────────┴────────────────────┘
```

## Component Details

### 1. Client Layer

#### USSD Interface
- **Target**: Feature phones (Nokia, Tecno, Itel)
- **Protocol**: USSD (Unstructured Supplementary Service Data)
- **Session**: Stateful, 180-second timeout
- **Features**: Registration, weather, prices, advisories
- **Languages**: All supported languages
- **Menu Structure**:
  ```
  *384*96#
  ├── 1. Weather Forecast
  ├── 2. Market Prices
  ├── 3. Crop Advisory
  ├── 4. Sell Produce
  └── 5. My Profile
  ```

#### SMS Interface
- **Target**: All mobile phones
- **Use Cases**: Alerts, confirmations, one-way notifications
- **Format**: Short code + keywords
- **Examples**:
  - `PRICE MAIZE` → Get maize prices
  - `WEATHER` → Get weather forecast
  - `SELL 100KG MAIZE` → List produce for sale

#### Mobile PWA
- **Target**: Smartphones (Android 8+, iOS 12+)
- **Technology**: React + TypeScript + TailwindCSS
- **Features**: Full functionality, offline sync, push notifications
- **Storage**: IndexedDB for offline data
- **Size**: <500KB initial bundle

#### Web Admin
- **Target**: Desktop browsers
- **Technology**: Next.js 14 + TypeScript
- **Features**: Dashboard, analytics, content management
- **Authentication**: Role-based access control (RBAC)

### 2. API Gateway

**Technology**: Kong or AWS API Gateway

**Responsibilities**:
- Authentication & Authorization (JWT)
- Rate limiting (100 req/min per user)
- Request/response transformation
- Load balancing across services
- SSL/TLS termination
- API versioning (/api/v1, /api/v2)

**Security Headers**:
```
Strict-Transport-Security: max-age=31536000
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self'
```

### 3. Core Services

#### API Service (NestJS)

**Port**: 3000
**Database**: PostgreSQL with PostGIS
**Cache**: Redis

**Modules**:
- `auth`: Authentication & authorization
- `farmers`: Farmer profile management
- `crops`: Crop information database
- `weather`: Weather data aggregation
- `market`: Market prices & connections
- `payments`: Payment processing
- `advisory`: Agricultural advisories
- `analytics`: Usage analytics

**Database Schema** (Key Tables):
```sql
-- Farmers
CREATE TABLE farmers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  phone_number VARCHAR(20) UNIQUE NOT NULL,
  name VARCHAR(255) NOT NULL,
  location GEOGRAPHY(POINT) NOT NULL,
  language VARCHAR(10) DEFAULT 'en',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Crops
CREATE TABLE crops (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  scientific_name VARCHAR(255),
  growing_season INTEGER, -- days
  water_requirement VARCHAR(50),
  soil_type VARCHAR(100)
);

-- Market Prices
CREATE TABLE market_prices (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  crop_id UUID REFERENCES crops(id),
  market_location VARCHAR(255),
  price_per_kg DECIMAL(10,2),
  currency VARCHAR(3) DEFAULT 'KES',
  date DATE NOT NULL,
  source VARCHAR(100)
);

-- Farmer Crops
CREATE TABLE farmer_crops (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  farmer_id UUID REFERENCES farmers(id),
  crop_id UUID REFERENCES crops(id),
  hectares DECIMAL(10,2),
  planting_date DATE,
  expected_harvest_date DATE
);
```

**API Endpoints**:
```
POST   /api/v1/auth/register
POST   /api/v1/auth/login
GET    /api/v1/farmers/me
PATCH  /api/v1/farmers/me
GET    /api/v1/crops
GET    /api/v1/weather?lat={lat}&lng={lng}
GET    /api/v1/market/prices?crop={crop}
POST   /api/v1/market/listings
GET    /api/v1/advisory?crop={crop}&season={season}
```

#### ML Service (Python/FastAPI)

**Port**: 5000
**Framework**: FastAPI + TensorFlow/PyTorch

**Models**:

1. **Crop Disease Detection**
   - Input: Image (224x224 RGB)
   - Output: Disease name + confidence + treatment
   - Model: MobileNetV2 (fine-tuned)
   - Accuracy: 92%
   - Inference: <500ms on CPU

2. **Yield Prediction**
   - Input: Crop type, soil data, weather, farming practices
   - Output: Expected yield in kg/hectare
   - Model: XGBoost ensemble
   - MAE: 8.5%

3. **Price Forecasting**
   - Input: Historical prices, seasonality, weather
   - Output: 30-day price forecast
   - Model: LSTM + attention
   - MAPE: 12%

**Endpoints**:
```
POST   /ml/v1/detect-disease
POST   /ml/v1/predict-yield
POST   /ml/v1/forecast-prices
GET    /ml/v1/models/status
```

#### USSD Gateway

**Port**: 3001
**Technology**: Node.js + Express

**Flow**:
```
User dials *384*96#
    ↓
Telco forwards to USSD Gateway
    ↓
Gateway creates session (180s TTL)
    ↓
Gateway renders menu from state machine
    ↓
User selects option
    ↓
Gateway calls Core API
    ↓
Gateway returns response to Telco
    ↓
Telco displays to user
```

**Session Management**:
- Storage: Redis (TTL: 180 seconds)
- Structure: `ussd:session:{sessionId}`
- Data: Current menu, user inputs, context

#### SMS Gateway

**Port**: 3002
**Technology**: Node.js + Express

**Features**:
- Keyword-based commands
- Scheduled bulk messages
- Delivery reports tracking
- Template management

**Message Types**:
1. **Transactional**: Order confirmations, payments
2. **Alerts**: Weather warnings, price changes
3. **Reminders**: Planting schedules, meetings
4. **Marketing**: New features, promotions

### 4. Data Layer

#### PostgreSQL Database

**Version**: 15 with PostGIS
**Configuration**:
- Connection pool: 20 connections
- Replication: 2 read replicas
- Backup: Daily full + hourly incrementals
- Retention: 30 days

**Optimizations**:
- Indexes on phone_number, location, crop_id
- Partitioning on date columns (by month)
- Materialized views for analytics

#### Redis Cache

**Version**: 7
**Use Cases**:
- Session storage (USSD, web)
- Rate limiting counters
- Real-time analytics
- Pub/Sub for notifications

**Key Patterns**:
```
user:session:{userId}        # User sessions
ussd:session:{sessionId}     # USSD sessions
cache:weather:{location}     # Weather cache (TTL: 1h)
cache:prices:{crop}          # Price cache (TTL: 6h)
ratelimit:{userId}:{endpoint} # Rate limiting
```

#### RabbitMQ

**Version**: 3.12
**Exchanges**:
- `notifications.topic` - Notification routing
- `payments.topic` - Payment events
- `analytics.topic` - Analytics events

**Queues**:
- `sms.outbound` - SMS to be sent
- `email.outbound` - Emails to be sent
- `push.outbound` - Push notifications
- `analytics.events` - Analytics processing

### 5. External Integrations

#### Weather APIs

**Primary**: OpenWeatherMap
**Fallback**: IBM Weather API

**Data Points**:
- 7-day forecast
- Hourly updates
- Rainfall probability
- Temperature min/max
- UV index
- Wind speed

**Caching**: 1 hour

#### Payment Gateways

**M-Pesa (Kenya)**:
- STK Push for payments
- C2B for collections
- B2C for payouts
- Transaction queries

**Airtel Money (Multiple countries)**:
- Payment API
- Disbursement API

**MTN MoMo (Multiple countries)**:
- Collections API
- Disbursements API

**Security**:
- PCI DSS Level 1 compliance
- Webhook signature verification
- Encrypted credentials in Vault

### 6. Infrastructure

#### Hosting

**Cloud Provider**: AWS (primary), Azure (DR)

**Compute**:
- EKS (Elastic Kubernetes Service)
- Node groups: 3-10 nodes (t3.medium)
- Auto-scaling based on CPU/memory

**Database**:
- RDS PostgreSQL Multi-AZ
- ElastiCache Redis cluster

**Storage**:
- S3 for user uploads
- CloudFront CDN for assets

**Networking**:
- VPC with public/private subnets
- NAT Gateway for outbound traffic
- Application Load Balancer
- Route53 for DNS

#### CI/CD Pipeline

**Tools**: GitHub Actions

**Workflow**:
```
Push to main
    ↓
Run linting & tests
    ↓
Build Docker images
    ↓
Push to ECR
    ↓
Update K8s manifests
    ↓
Rolling deployment (staging)
    ↓
Smoke tests
    ↓
Manual approval
    ↓
Rolling deployment (production)
```

**Deployment Strategy**:
- Rolling updates (zero downtime)
- Canary deployments for major changes
- Blue-green for database migrations

#### Monitoring & Logging

**Monitoring**: Prometheus + Grafana
**Logging**: ELK Stack (Elasticsearch, Logstash, Kibana)
**Tracing**: Jaeger for distributed tracing
**Alerting**: PagerDuty

**Key Metrics**:
- API response time (p50, p95, p99)
- Error rate (4xx, 5xx)
- Database query time
- Cache hit ratio
- USSD session completion rate
- Payment success rate

**Alerts**:
- API error rate > 5%
- Response time > 2s
- Database connections > 80%
- Disk usage > 85%

## Security Architecture

### Authentication

**Method**: JWT (JSON Web Tokens)
**Flow**:
1. User logs in with phone + OTP
2. Server validates OTP
3. Server issues access token (15 min) + refresh token (30 days)
4. Client includes access token in requests
5. Server validates token signature and expiry

**Token Structure**:
```json
{
  "userId": "uuid",
  "phone": "+254712345678",
  "role": "farmer",
  "iat": 1234567890,
  "exp": 1234568790
}
```

### Authorization

**Model**: Role-Based Access Control (RBAC)

**Roles**:
- `farmer`: Regular farmer
- `buyer`: Produce buyer
- `agent`: Extension agent
- `admin`: System administrator

**Permissions**:
```typescript
const permissions = {
  farmer: ['read:own_profile', 'update:own_profile', 'create:listing'],
  buyer: ['read:listings', 'create:order', 'read:own_orders'],
  agent: ['read:farmers', 'create:advisory', 'read:analytics'],
  admin: ['*'] // All permissions
};
```

### Data Protection

**Encryption**:
- At rest: AES-256
- In transit: TLS 1.3
- Database: Encrypted volumes
- Backups: Encrypted S3 buckets

**PII Handling**:
- Phone numbers hashed in logs
- Minimal data collection
- Data retention policies (GDPR)
- Right to deletion

## Scalability & Performance

### Horizontal Scaling

**Services**:
- API: 3-10 replicas
- USSD Gateway: 2-5 replicas
- SMS Gateway: 2-5 replicas
- ML Service: 1-3 replicas (GPU instances)

**Triggers**:
- CPU > 70%
- Memory > 80%
- Request queue depth > 100

### Caching Strategy

**Layers**:
1. Browser cache (PWA): 24 hours
2. CDN cache (CloudFront): 7 days
3. Redis cache: Variable TTL
4. Database query cache: Disabled (consistency)

**Cache Keys**:
```
weather:{lat},{lng}:{date}
prices:{crop}:{market}:{date}
crops:{id}
farmer:{id}
```

### Database Optimization

**Indexes**:
```sql
-- Critical indexes
CREATE INDEX idx_farmers_phone ON farmers(phone_number);
CREATE INDEX idx_farmers_location ON farmers USING GIST(location);
CREATE INDEX idx_market_prices_crop_date ON market_prices(crop_id, date);
CREATE INDEX idx_farmer_crops_farmer ON farmer_crops(farmer_id);
```

**Query Optimization**:
- Use explain analyze for slow queries
- Limit result sets
- Avoid N+1 queries
- Use materialized views for complex aggregations

## Disaster Recovery

**RTO** (Recovery Time Objective): 1 hour
**RPO** (Recovery Point Objective): 15 minutes

**Backup Strategy**:
- Database: Automated daily snapshots + 15-min WAL backups
- Redis: Daily RDB snapshots
- S3: Versioning enabled + cross-region replication

**Failover Plan**:
1. Monitor detects primary region failure
2. PagerDuty alerts on-call engineer
3. Engineer triggers failover to DR region
4. DNS updated to point to DR
5. Services restart in DR region
6. Backups restored if needed

**Regular Drills**: Quarterly failover tests

## Future Enhancements

### Phase 2 (Q2 2026)
- Voice assistant (speech recognition)
- Blockchain supply chain tracking
- Drone imagery integration
- Carbon credit marketplace

### Phase 3 (Q4 2026)
- IoT sensor integration (soil moisture, temperature)
- Satellite imagery for crop health
- Automated irrigation systems
- Predictive pest outbreak alerts

## Questions?

Contact: architecture@farmconnect.africa
