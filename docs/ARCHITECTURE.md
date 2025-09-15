# System Architecture
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Claude (Tech Lead)  
**Review Status:** APPROVED  

---

## Architecture Overview

### System Goals
- **Multi-tenant SaaS**: Isolated data per organization with shared infrastructure
- **AI-first**: Every business function powered by intelligent agents
- **Mobile-optimized**: Primary interface through responsive web app
- **Scalable**: Handle 10K+ companies with real-time interactions
- **Observable**: Full telemetry and monitoring for all components

### High-Level Architecture (C4 Context)

```mermaid
C4Context
    title AI Departments Platform - System Context

    Person(entrepreneur, "Micro-Entrepreneur", "Uses platform to run business")
    Person(customer, "End Customer", "Interacts via WhatsApp/Social")
    
    System(platform, "AI Departments Platform", "Multi-tenant SaaS for business automation")
    
    System_Ext(whatsapp, "WhatsApp Cloud API", "Customer messaging")
    System_Ext(social, "Social Media APIs", "Instagram, Facebook, TikTok")
    System_Ext(ai, "AI Providers", "OpenAI, Anthropic, Google")
    System_Ext(payments, "Payment Processors", "Stripe, Mercado Pago")
    System_Ext(storage, "Cloud Storage", "Google Cloud Storage")
    
    Rel(entrepreneur, platform, "Manages business")
    Rel(customer, whatsapp, "Sends messages")
    Rel(customer, social, "Engages with content")
    
    Rel(platform, whatsapp, "Automated responses")
    Rel(platform, social, "Content publishing")
    Rel(platform, ai, "Content generation")
    Rel(platform, payments, "Billing")
    Rel(platform, storage, "Asset storage")
```

---

## Container Architecture (C4 Container)

### Frontend Layer

#### Web Application (Next.js 14)
- **Technology**: React 18 + TypeScript + Tailwind CSS
- **Hosting**: Vercel (primary) / GCP Cloud Run (backup)
- **Features**: 
  - Server-side rendering for SEO
  - Progressive Web App capabilities
  - Mobile-first responsive design
  - Real-time updates via WebSockets

#### Mobile Web App
- **Approach**: Responsive web app with native-like experience
- **PWA Features**: Offline capability, push notifications, install prompts
- **Performance**: <3 second load times, <1 second navigation
- **Future**: Native iOS/Android apps in Phase 2

### Backend Services

#### API Gateway (FastAPI)
- **Technology**: Python 3.11 + FastAPI + Pydantic
- **Hosting**: GCP Cloud Run with auto-scaling
- **Features**:
  - OpenAPI documentation
  - JWT authentication
  - Rate limiting per tenant
  - Request/response validation
  - CORS handling for web clients

#### Department Orchestrator
- **Technology**: Python + LangGraph + Redis
- **Purpose**: Coordinates AI agents within departments
- **Features**:
  - Multi-agent workflow execution
  - State management across agent interactions
  - Error handling and compensation
  - Performance monitoring per agent

#### Real-time Services
- **Technology**: WebSocket server + Redis Pub/Sub
- **Purpose**: Live updates for dashboards and notifications
- **Features**:
  - Department status updates
  - Content generation progress
  - Customer message notifications
  - System health alerts

### Data Layer

#### Primary Database (PostgreSQL 15)
- **Hosting**: GCP Cloud SQL with high availability
- **Size**: Start with 4 vCPU, 16GB RAM, scale as needed
- **Features**:
  - Multi-tenant schema design
  - Encrypted at rest and in transit
  - Automated backups with point-in-time recovery
  - Read replicas for analytics

#### Cache Layer (Redis 7)
- **Hosting**: GCP Memorystore
- **Use Cases**:
  - API response caching
  - Session storage
  - AI agent state management
  - Rate limiting counters
  - Real-time messaging

#### Vector Database (pgvector)
- **Purpose**: AI embeddings for RAG and semantic search
- **Use Cases**:
  - Company knowledge base
  - Content similarity matching
  - Customer inquiry classification
  - Brand voice consistency

### External Integrations

#### AI Services
- **Primary**: OpenAI GPT-4 for content generation
- **Secondary**: Anthropic Claude for complex reasoning
- **Fallback**: Google PaLM for cost optimization
- **Image Generation**: DALL-E 3 + Stable Diffusion

#### Communication APIs
- **WhatsApp**: Cloud API for message automation
- **Social Media**: Instagram Basic Display + Facebook Graph API
- **Email**: SendGrid for transactional emails
- **SMS**: Twilio for notifications (Brazil-focused)

#### Payment Processing
- **Brazil**: Mercado Pago (primary), Stripe (secondary)
- **International**: Stripe (primary), local processors
- **Features**: Subscription management, webhook handling, tax calculation

---

## Component Architecture (C4 Component)

### API Gateway Components

```typescript
// Core API structure
interface APIGateway {
  authentication: AuthMiddleware;
  rateLimiting: RateLimiter;
  routing: Router;
  validation: RequestValidator;
  monitoring: MetricsCollector;
}

interface AuthMiddleware {
  jwtVerification: (token: string) => UserClaims;
  tenantIsolation: (userId: string) => OrganizationId;
  roleBasedAccess: (user: UserClaims, resource: string) => boolean;
}
```

#### Auth & Tenant Management
- **JWT tokens**: 15-minute access + 7-day refresh
- **Multi-tenancy**: Organization-scoped data access
- **RBAC**: Owner, Admin, Member roles per organization
- **Session management**: Redis-backed with sliding expiration

#### API Endpoints Structure
```
/api/v1/
├── auth/                 # Authentication & user management
├── organizations/        # Company/tenant management
├── departments/          # Department configuration & status
├── agents/              # Individual agent management
├── content/             # Generated content & assets
├── integrations/        # External service connections
├── analytics/           # Usage metrics & insights
├── billing/             # Subscription & payment management
└── webhooks/            # External service callbacks
```

### Department Orchestrator Components

#### Agent Coordination Engine
```python
class DepartmentOrchestrator:
    def __init__(self):
        self.agent_registry = AgentRegistry()
        self.workflow_engine = LangGraphWorkflow()
        self.state_manager = RedisStateManager()
        self.monitoring = AgentMonitoring()
    
    async def execute_department_workflow(
        self, 
        department_id: str, 
        trigger: WorkflowTrigger
    ) -> WorkflowResult:
        # Coordinate multiple agents for business function
        pass
```

#### Agent Types by Department

**Marketing Department Agents:**
- `SocialMediaAgent`: Content creation and scheduling
- `SEOAgent`: Blog content and keyword optimization  
- `EmailAgent`: Newsletter and automation sequences
- `BrandAgent`: Voice and visual consistency

**Customer Service Department Agents:**
- `WhatsAppAgent`: Message classification and responses
- `FAQAgent`: Knowledge base management
- `EscalationAgent`: Human handoff decisions
- `SentimentAgent`: Customer satisfaction monitoring

### Data Access Layer

#### Repository Pattern Implementation
```python
class TenantRepository:
    def __init__(self, db: Database, tenant_id: str):
        self.db = db
        self.tenant_id = tenant_id
    
    async def find_by_id(self, entity_id: str) -> Optional[Entity]:
        # Automatic tenant filtering on all queries
        return await self.db.query(
            "SELECT * FROM entities WHERE id = $1 AND organization_id = $2",
            entity_id, self.tenant_id
        )
```

#### Database Schema Design
```sql
-- Multi-tenant base schema
CREATE SCHEMA IF NOT EXISTS platform;

-- Core tenant management
CREATE TABLE platform.organizations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- All business entities include organization_id
CREATE TABLE platform.departments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id),
    type VARCHAR(50) NOT NULL, -- 'marketing', 'customer_service', etc.
    configuration JSONB NOT NULL DEFAULT '{}',
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

---

## Infrastructure Architecture

### Google Cloud Platform Setup

#### Compute Resources
```yaml
# Cloud Run Services
services:
  api-gateway:
    image: gcr.io/project/api-gateway
    cpu: 2
    memory: 4Gi
    min_instances: 2
    max_instances: 100
    concurrency: 100
    
  department-orchestrator:
    image: gcr.io/project/orchestrator
    cpu: 4
    memory: 8Gi
    min_instances: 1
    max_instances: 50
    concurrency: 10
    
  websocket-server:
    image: gcr.io/project/websocket
    cpu: 1
    memory: 2Gi
    min_instances: 1
    max_instances: 10
    concurrency: 1000
```

#### Data Storage
```yaml
# Cloud SQL (PostgreSQL)
database:
  tier: db-custom-4-16384  # 4 vCPU, 16GB RAM
  storage: 100GB SSD
  backup_enabled: true
  point_in_time_recovery: true
  high_availability: true
  
# Memorystore (Redis)
cache:
  tier: M1  # 1GB memory
  auth_enabled: true
  transit_encryption: true
  
# Cloud Storage
storage:
  buckets:
    - user-assets: Multi-regional, public read
    - system-backups: Regional, private
    - ai-models: Regional, private
```

#### Networking & Security
```yaml
# VPC Configuration
network:
  vpc_name: ai-departments-vpc
  subnets:
    - name: web-tier
      cidr: 10.0.1.0/24
      region: us-central1
    - name: app-tier  
      cidr: 10.0.2.0/24
      region: us-central1
    - name: data-tier
      cidr: 10.0.3.0/24
      region: us-central1

# Security
security:
  ssl_certificates: managed
  cloud_armor: enabled
  private_google_access: enabled
  firewall_rules:
    - allow_https_ingress
    - allow_internal_communication
    - deny_all_other
```

### Monitoring & Observability

#### OpenTelemetry Implementation
```python
# Distributed tracing setup
from opentelemetry import trace
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor
from opentelemetry.instrumentation.asyncpg import AsyncPGInstrumentor

# Auto-instrument FastAPI and database calls
FastAPIInstrumentor.instrument_app(app)
AsyncPGInstrumentor().instrument()

# Custom business metrics
@app.middleware("http")
async def add_business_metrics(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    
    # Track department-specific metrics
    if request.url.path.startswith("/api/v1/departments"):
        department_request_duration.observe(
            time.time() - start_time,
            labels={"department": extract_department_type(request)}
        )
    
    return response
```

#### Key Metrics & Alerts
```yaml
# SLI/SLO Monitoring
metrics:
  api_latency:
    sli: 95th percentile response time
    slo: <500ms for 99% of requests
    alert_threshold: >1000ms for 5 minutes
    
  department_execution:
    sli: Time from trigger to completion
    slo: <8 seconds for content generation
    alert_threshold: >15 seconds for 3 minutes
    
  ai_model_availability:
    sli: Successful AI API calls
    slo: >99% success rate
    alert_threshold: <95% success for 2 minutes
    
  customer_impact:
    sli: End-to-end workflow success
    slo: >98% successful completions
    alert_threshold: <95% success for 5 minutes
```

---

## Security Architecture

### Authentication & Authorization

#### JWT Implementation
```python
# JWT token structure
class TokenClaims:
    user_id: str
    organization_id: str
    roles: List[str]
    permissions: List[str]
    iat: int  # issued at
    exp: int  # expires at
    aud: str = "ai-departments-platform"
    iss: str = "api.aidepartments.com"

# RBAC enforcement
class PermissionMiddleware:
    async def check_permissions(
        self, 
        user: TokenClaims, 
        resource: str, 
        action: str
    ) -> bool:
        # Check user roles and explicit permissions
        required_permission = f"{resource}:{action}"
        return (
            required_permission in user.permissions or
            self.role_has_permission(user.roles, required_permission)
        )
```

#### Data Isolation Strategy
```sql
-- Row-level security for multi-tenancy
ALTER TABLE platform.departments ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation ON platform.departments
    FOR ALL TO application_role
    USING (organization_id = current_setting('app.current_organization')::UUID);

-- Set tenant context in each request
SET app.current_organization = '{organization_id}';
```

### Data Protection

#### Encryption Strategy
- **Data at Rest**: AES-256 encryption for all databases and storage
- **Data in Transit**: TLS 1.3 for all API communications
- **Application Secrets**: GCP Secret Manager with automatic rotation
- **Customer Data**: Field-level encryption for sensitive information

#### Privacy & Compliance (LGPD)
```python
class DataPrivacyManager:
    def __init__(self):
        self.encryption_key = self.get_organization_key()
        self.audit_logger = AuditLogger()
    
    async def store_sensitive_data(
        self, 
        data: SensitiveData, 
        legal_basis: LGPDLegalBasis
    ) -> str:
        # Encrypt and audit all sensitive data storage
        encrypted_data = self.encrypt(data)
        await self.audit_logger.log_data_processing(
            action="store",
            data_type=data.type,
            legal_basis=legal_basis,
            retention_period=self.calculate_retention(data.type)
        )
        return await self.store(encrypted_data)
```

---

## Performance & Scalability

### Scaling Strategy

#### Horizontal Scaling
- **API Gateway**: Auto-scale based on CPU and request rate
- **Department Orchestrator**: Scale based on queue depth
- **WebSocket Server**: Scale based on concurrent connections
- **Database**: Read replicas for analytics, connection pooling

#### Caching Strategy
```python
# Multi-level caching
class CacheManager:
    def __init__(self):
        self.l1_cache = InMemoryCache(ttl=60)  # Fast, local
        self.l2_cache = RedisCache(ttl=3600)   # Shared, durable
        self.l3_cache = CDNCache(ttl=86400)    # Global, static
    
    async def get_cached_response(self, key: str) -> Optional[dict]:
        # Check caches in order of speed
        if value := await self.l1_cache.get(key):
            return value
        if value := await self.l2_cache.get(key):
            await self.l1_cache.set(key, value)
            return value
        return None
```

#### Performance Targets
```yaml
# Performance SLAs
performance_targets:
  api_response_time:
    p50: <200ms
    p95: <500ms
    p99: <1000ms
    
  page_load_time:
    first_contentful_paint: <1.5s
    largest_contentful_paint: <2.5s
    cumulative_layout_shift: <0.1
    
  ai_generation_time:
    social_post: <8s
    whatsapp_response: <3s
    image_generation: <15s
    
  database_query_time:
    simple_queries: <10ms
    complex_queries: <100ms
    analytics_queries: <1000ms
```

### Queue Management

#### Asynchronous Processing
```python
# Background job processing
class DepartmentJobQueue:
    def __init__(self):
        self.redis = Redis()
        self.workers = []
    
    async def enqueue_content_generation(
        self, 
        department_id: str, 
        prompt: str, 
        priority: JobPriority = JobPriority.NORMAL
    ) -> JobId:
        job = ContentGenerationJob(
            department_id=department_id,
            prompt=prompt,
            priority=priority,
            created_at=datetime.utcnow()
        )
        
        queue_name = f"content_generation_{priority.value}"
        await self.redis.lpush(queue_name, job.serialize())
        return job.id
```

---

## Disaster Recovery & Business Continuity

### Backup Strategy
```yaml
# Automated backup configuration
backup_strategy:
  database:
    frequency: every_6_hours
    retention: 30_days
    cross_region: true
    encryption: enabled
    
  file_storage:
    frequency: daily
    retention: 90_days
    versioning: enabled
    lifecycle: archive_after_30_days
    
  application_state:
    frequency: real_time
    method: redis_persistence
    backup_frequency: hourly
```

### Failover Procedures
```yaml
# Multi-region disaster recovery
disaster_recovery:
  rpo: 1_hour  # Maximum data loss
  rto: 30_minutes  # Maximum downtime
  
  primary_region: us-central1
  backup_region: us-east1
  
  failover_triggers:
    - region_wide_outage
    - database_corruption
    - security_breach
    
  automated_failover:
    enabled: true
    health_check_interval: 30s
    failure_threshold: 3_consecutive_failures
```

### Monitoring & Alerting
```yaml
# Critical system monitoring
alerts:
  severity_levels:
    critical:
      response_time: immediate
      escalation: 5_minutes
      channels: [pagerduty, slack, sms]
      
    warning:
      response_time: 15_minutes
      escalation: 30_minutes
      channels: [slack, email]
      
  alert_conditions:
    - name: api_gateway_down
      condition: http_success_rate < 95%
      duration: 2_minutes
      severity: critical
      
    - name: high_ai_latency
      condition: ai_response_time > 15_seconds
      duration: 5_minutes
      severity: warning
      
    - name: database_connection_pool_exhausted
      condition: db_active_connections > 80%
      duration: 1_minute
      severity: critical
```

---

## Development & Deployment

### Environment Strategy
```yaml
# Multi-environment setup
environments:
  development:
    purpose: Local development and testing
    resources: minimal
    data: synthetic
    ai_models: development_tier
    
  staging:
    purpose: Integration testing and demos
    resources: production_like
    data: anonymized_production_subset
    ai_models: production_tier
    
  production:
    purpose: Live customer workloads
    resources: auto_scaling
    data: encrypted_customer_data
    ai_models: production_tier
    monitoring: full_observability
```

### CI/CD Pipeline
```yaml
# GitHub Actions workflow
deployment_pipeline:
  triggers:
    - push_to_main
    - pull_request_approved
    - manual_deployment
    
  stages:
    1_code_quality:
      - lint_check
      - type_check
      - security_scan
      - dependency_audit
      
    2_testing:
      - unit_tests
      - integration_tests
      - e2e_tests
      - performance_tests
      
    3_build:
      - docker_build
      - vulnerability_scan
      - registry_push
      
    4_deploy:
      - staging_deployment
      - smoke_tests
      - production_deployment
      - health_checks
```

---

## Technology Decisions & Rationale

### Framework Choices

#### Frontend: Next.js 14
**Rationale:**
- Server-side rendering for SEO and performance
- React ecosystem for rich interactivity
- Built-in optimization and deployment
- Strong TypeScript support

#### Backend: FastAPI
**Rationale:**
- Async/await native support for high concurrency
- Automatic OpenAPI documentation
- Built-in validation with Pydantic
- High performance compared to Django/Flask

#### Database: PostgreSQL + Redis
**Rationale:**
- PostgreSQL: ACID compliance, complex queries, JSON support
- Redis: Fast caching, pub/sub, session storage
- pgvector: Native vector operations for AI embeddings

#### AI Orchestration: LangGraph
**Rationale:**
- Multi-agent coordination capabilities
- State management across complex workflows
- Integration with major AI providers
- Python ecosystem compatibility

### Cloud Provider: Google Cloud Platform
**Rationale:**
- Strong AI/ML services integration
- Cost-effective for our scale
- Excellent auto-scaling capabilities
- Robust security and compliance features
- Good performance in Brazil/LATAM

---

**Document Status:** Architecture approved for MVP implementation. Review quarterly for scaling adjustments.