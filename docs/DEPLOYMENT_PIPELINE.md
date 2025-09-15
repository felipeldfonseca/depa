# Deployment Pipeline & CI/CD
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Felipe PM + Claude  
**Status:** APPROVED  

---

## Pipeline Overview

### Deployment Strategy
- **Multi-Stage Pipeline**: Development → Staging → Production
- **Blue-Green Deployment**: Zero-downtime production deployments
- **Feature Flags**: Gradual rollout and quick rollback capabilities
- **Infrastructure as Code**: Terraform for GCP resources
- **Container-First**: Docker containers orchestrated via Cloud Run

### Technology Stack
- **CI/CD Platform**: GitHub Actions
- **Container Registry**: Google Artifact Registry
- **Orchestration**: Google Cloud Run
- **Infrastructure**: Terraform + Google Cloud
- **Monitoring**: OpenTelemetry, Prometheus, Grafana
- **Secret Management**: Google Secret Manager

---

## Repository Structure

### Monorepo Layout
```
business-ai/
├── .github/
│   └── workflows/
│       ├── ci.yml                    # Continuous Integration
│       ├── deploy-staging.yml        # Staging deployment
│       ├── deploy-production.yml     # Production deployment
│       ├── security-scan.yml         # Security scanning
│       └── infrastructure.yml        # Infrastructure updates
├── frontend/                         # Next.js application
│   ├── Dockerfile
│   ├── package.json
│   └── src/
├── backend/                          # FastAPI application
│   ├── Dockerfile
│   ├── requirements.txt
│   └── app/
├── infrastructure/                   # Terraform configurations
│   ├── environments/
│   │   ├── dev/
│   │   ├── staging/
│   │   └── production/
│   └── modules/
├── k8s/                             # Kubernetes manifests
│   ├── base/
│   └── overlays/
├── scripts/                         # Deployment scripts
│   ├── build.sh
│   ├── deploy.sh
│   └── migrate.sh
└── docker-compose.yml               # Local development
```

---

## Continuous Integration Pipeline

### GitHub Actions CI Workflow
```yaml
# .github/workflows/ci.yml
name: Continuous Integration

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '18'
  PYTHON_VERSION: '3.11'
  REGISTRY: gcr.io
  PROJECT_ID: ai-departments-prod

jobs:
  test-frontend:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
          cache-dependency-path: frontend/package-lock.json
          
      - name: Install dependencies
        working-directory: ./frontend
        run: npm ci
        
      - name: Run linting
        working-directory: ./frontend
        run: npm run lint
        
      - name: Run type checking
        working-directory: ./frontend
        run: npm run type-check
        
      - name: Run tests
        working-directory: ./frontend
        run: npm run test:ci
        
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./frontend/coverage/lcov.info
          flags: frontend

  test-backend:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: test_db
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      redis:
        image: redis:7
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
          
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: ${{ env.PYTHON_VERSION }}
          
      - name: Install dependencies
        working-directory: ./backend
        run: |
          pip install -r requirements.txt
          pip install -r requirements-dev.txt
          
      - name: Run linting
        working-directory: ./backend
        run: |
          ruff check .
          black --check .
          mypy app/
          
      - name: Run tests
        working-directory: ./backend
        run: pytest --cov=app --cov-report=xml
        env:
          DATABASE_URL: postgresql://postgres:postgres@localhost/test_db
          REDIS_URL: redis://localhost:6379/0
          
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./backend/coverage.xml
          flags: backend

  security-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'
          
      - name: Upload Trivy scan results
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
          
      - name: Dependency scan (frontend)
        working-directory: ./frontend
        run: npm audit --audit-level=high
        
      - name: Dependency scan (backend)
        working-directory: ./backend
        run: |
          pip install safety
          safety check

  build-images:
    needs: [test-frontend, test-backend, security-scan]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Setup Google Cloud
        uses: google-github-actions/setup-gcloud@v1
        with:
          service_account_key: ${{ secrets.GCP_SERVICE_ACCOUNT_KEY }}
          project_id: ${{ env.PROJECT_ID }}
          
      - name: Configure Docker
        run: gcloud auth configure-docker gcr.io
        
      - name: Build and push frontend image
        working-directory: ./frontend
        run: |
          docker build -t ${{ env.REGISTRY }}/${{ env.PROJECT_ID }}/frontend:${{ github.sha }} .
          docker push ${{ env.REGISTRY }}/${{ env.PROJECT_ID }}/frontend:${{ github.sha }}
          
      - name: Build and push backend image
        working-directory: ./backend
        run: |
          docker build -t ${{ env.REGISTRY }}/${{ env.PROJECT_ID }}/backend:${{ github.sha }} .
          docker push ${{ env.REGISTRY }}/${{ env.PROJECT_ID }}/backend:${{ github.sha }}
          
      - name: Update staging deployment
        if: github.ref == 'refs/heads/develop'
        uses: ./.github/actions/deploy
        with:
          environment: staging
          frontend_image: ${{ env.REGISTRY }}/${{ env.PROJECT_ID }}/frontend:${{ github.sha }}
          backend_image: ${{ env.REGISTRY }}/${{ env.PROJECT_ID }}/backend:${{ github.sha }}
```

---

## Staging Deployment Pipeline

### Staging Workflow
```yaml
# .github/workflows/deploy-staging.yml
name: Deploy to Staging

on:
  push:
    branches: [develop]
  workflow_dispatch:

env:
  PROJECT_ID: ai-departments-staging
  REGION: us-central1

jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    environment: staging
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Setup Google Cloud
        uses: google-github-actions/setup-gcloud@v1
        with:
          service_account_key: ${{ secrets.STAGING_GCP_SERVICE_ACCOUNT_KEY }}
          project_id: ${{ env.PROJECT_ID }}
          
      - name: Deploy database migrations
        run: |
          gcloud sql connect ai-departments-staging-db --user=postgres < migrations/latest.sql
          
      - name: Deploy backend service
        run: |
          gcloud run deploy backend \
            --image=gcr.io/${{ env.PROJECT_ID }}/backend:${{ github.sha }} \
            --platform=managed \
            --region=${{ env.REGION }} \
            --allow-unauthenticated \
            --set-env-vars="DATABASE_URL=${{ secrets.STAGING_DATABASE_URL }}" \
            --set-env-vars="REDIS_URL=${{ secrets.STAGING_REDIS_URL }}" \
            --memory=1Gi \
            --cpu=1 \
            --max-instances=10 \
            --timeout=300
            
      - name: Deploy frontend service
        run: |
          gcloud run deploy frontend \
            --image=gcr.io/${{ env.PROJECT_ID }}/frontend:${{ github.sha }} \
            --platform=managed \
            --region=${{ env.REGION }} \
            --allow-unauthenticated \
            --set-env-vars="NEXT_PUBLIC_API_URL=https://api-staging.aidepartments.com.br" \
            --memory=512Mi \
            --cpu=1 \
            --max-instances=5 \
            --timeout=60
            
      - name: Run integration tests
        run: |
          npm run test:integration:staging
          
      - name: Notify deployment
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          channel: '#deployments'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
        if: always()
```

---

## Production Deployment Pipeline

### Blue-Green Production Deployment
```yaml
# .github/workflows/deploy-production.yml
name: Deploy to Production

on:
  release:
    types: [published]
  workflow_dispatch:
    inputs:
      version:
        description: 'Version to deploy'
        required: true
        default: 'latest'

env:
  PROJECT_ID: ai-departments-prod
  REGION: us-central1

jobs:
  pre-deployment-checks:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Verify staging health
        run: |
          curl -f https://api-staging.aidepartments.com.br/health || exit 1
          
      - name: Run security scan
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'image'
          image-ref: 'gcr.io/${{ env.PROJECT_ID }}/backend:${{ github.sha }}'
          
      - name: Database migration dry-run
        run: |
          # Test migrations against staging replica
          ./scripts/test-migrations.sh
          
  deploy-production:
    needs: pre-deployment-checks
    runs-on: ubuntu-latest
    environment: production
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Setup Google Cloud
        uses: google-github-actions/setup-gcloud@v1
        with:
          service_account_key: ${{ secrets.PROD_GCP_SERVICE_ACCOUNT_KEY }}
          project_id: ${{ env.PROJECT_ID }}
          
      - name: Enable maintenance mode
        run: |
          gcloud run services replace-traffic frontend \
            --to-tags=maintenance:100 \
            --region=${{ env.REGION }}
            
      - name: Deploy database migrations
        run: |
          # Backup database before migration
          gcloud sql export sql ai-departments-prod-db \
            gs://ai-departments-backups/pre-migration-$(date +%Y%m%d%H%M%S).sql
          
          # Run migrations
          ./scripts/migrate-production.sh
          
      - name: Deploy new backend (blue environment)
        run: |
          gcloud run deploy backend-blue \
            --image=gcr.io/${{ env.PROJECT_ID }}/backend:${{ github.sha }} \
            --platform=managed \
            --region=${{ env.REGION }} \
            --no-traffic \
            --tag=blue \
            --set-env-vars="DATABASE_URL=${{ secrets.PROD_DATABASE_URL }}" \
            --memory=2Gi \
            --cpu=2 \
            --max-instances=50 \
            --min-instances=2 \
            --timeout=300
            
      - name: Deploy new frontend (blue environment)
        run: |
          gcloud run deploy frontend-blue \
            --image=gcr.io/${{ env.PROJECT_ID }}/frontend:${{ github.sha }} \
            --platform=managed \
            --region=${{ env.REGION }} \
            --no-traffic \
            --tag=blue \
            --set-env-vars="NEXT_PUBLIC_API_URL=https://api.aidepartments.com.br" \
            --memory=1Gi \
            --cpu=1 \
            --max-instances=20 \
            --min-instances=2 \
            --timeout=60
            
      - name: Health check (blue environment)
        run: |
          # Wait for services to be ready
          sleep 30
          
          # Test blue environment
          BLUE_URL=$(gcloud run services describe backend-blue \
            --region=${{ env.REGION }} \
            --format="value(status.url)")
          
          curl -f $BLUE_URL/health || exit 1
          
      - name: Gradual traffic switch
        run: |
          # Switch 10% traffic to blue
          gcloud run services update-traffic backend \
            --to-revisions=backend-blue:10,backend:90 \
            --region=${{ env.REGION }}
            
          gcloud run services update-traffic frontend \
            --to-revisions=frontend-blue:10,frontend:90 \
            --region=${{ env.REGION }}
            
          # Monitor for 5 minutes
          sleep 300
          
          # Check error rates
          ./scripts/check-error-rates.sh || exit 1
          
          # Switch 50% traffic
          gcloud run services update-traffic backend \
            --to-revisions=backend-blue:50,backend:50 \
            --region=${{ env.REGION }}
            
          gcloud run services update-traffic frontend \
            --to-revisions=frontend-blue:50,frontend:50 \
            --region=${{ env.REGION }}
            
          sleep 300
          ./scripts/check-error-rates.sh || exit 1
          
          # Switch 100% traffic
          gcloud run services update-traffic backend \
            --to-revisions=backend-blue:100 \
            --region=${{ env.REGION }}
            
          gcloud run services update-traffic frontend \
            --to-revisions=frontend-blue:100 \
            --region=${{ env.REGION }}
            
      - name: Post-deployment verification
        run: |
          # Wait for traffic to stabilize
          sleep 180
          
          # Run production smoke tests
          npm run test:smoke:production
          
          # Check all critical endpoints
          ./scripts/verify-production.sh
          
      - name: Cleanup old revision
        run: |
          # Remove old green environment after successful deployment
          gcloud run revisions delete backend-green --region=${{ env.REGION }} --quiet || true
          gcloud run revisions delete frontend-green --region=${{ env.REGION }} --quiet || true
          
  rollback-on-failure:
    needs: deploy-production
    runs-on: ubuntu-latest
    if: failure()
    
    steps:
      - name: Emergency rollback
        run: |
          # Switch traffic back to previous version
          gcloud run services update-traffic backend \
            --to-revisions=backend:100 \
            --region=${{ env.REGION }}
            
          gcloud run services update-traffic frontend \
            --to-revisions=frontend:100 \
            --region=${{ env.REGION }}
            
      - name: Alert team
        uses: 8398a7/action-slack@v3
        with:
          status: 'failure'
          channel: '#alerts'
          text: '🚨 Production deployment failed and rolled back'
          webhook_url: ${{ secrets.SLACK_WEBHOOK_ALERTS }}
```

---

## Infrastructure as Code

### Terraform Configuration
```hcl
# infrastructure/environments/production/main.tf
terraform {
  required_version = ">= 1.0"
  
  backend "gcs" {
    bucket = "ai-departments-terraform-state"
    prefix = "production"
  }
  
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 4.0"
    }
  }
}

provider "google" {
  project = var.project_id
  region  = var.region
}

# Cloud Run Services
module "backend_service" {
  source = "../../modules/cloud-run"
  
  name         = "backend"
  image        = var.backend_image
  port         = 8000
  cpu          = "2"
  memory       = "2Gi"
  min_replicas = 2
  max_replicas = 50
  
  env_vars = {
    DATABASE_URL = google_secret_manager_secret_version.database_url.secret_data
    REDIS_URL    = google_secret_manager_secret_version.redis_url.secret_data
    ENVIRONMENT  = "production"
  }
  
  vpc_connector_name = google_vpc_access_connector.main.name
}

module "frontend_service" {
  source = "../../modules/cloud-run"
  
  name         = "frontend"
  image        = var.frontend_image
  port         = 3000
  cpu          = "1"
  memory       = "1Gi"
  min_replicas = 2
  max_replicas = 20
  
  env_vars = {
    NEXT_PUBLIC_API_URL = "https://api.aidepartments.com.br"
    ENVIRONMENT         = "production"
  }
}

# Database
resource "google_sql_database_instance" "main" {
  name             = "ai-departments-prod"
  database_version = "POSTGRES_15"
  region           = var.region
  
  settings {
    tier                        = "db-custom-4-16384"
    availability_type           = "REGIONAL"
    disk_type                   = "PD_SSD"
    disk_size                   = 100
    disk_autoresize            = true
    disk_autoresize_limit      = 500
    
    backup_configuration {
      enabled                        = true
      start_time                     = "02:00"
      point_in_time_recovery_enabled = true
      transaction_log_retention_days = 7
      backup_retention_settings {
        retained_backups = 30
        retention_unit   = "COUNT"
      }
    }
    
    maintenance_window {
      day          = 7
      hour         = 3
      update_track = "stable"
    }
    
    database_flags {
      name  = "max_connections"
      value = "200"
    }
    
    ip_configuration {
      ipv4_enabled    = false
      private_network = google_compute_network.main.id
      require_ssl     = true
    }
  }
  
  deletion_protection = true
}

# Redis (Memorystore)
resource "google_redis_instance" "main" {
  name           = "ai-departments-redis"
  memory_size_gb = 4
  region         = var.region
  tier           = "STANDARD_HA"
  
  authorized_network = google_compute_network.main.id
  
  redis_configs = {
    maxmemory-policy = "allkeys-lru"
  }
}

# VPC and Networking
resource "google_compute_network" "main" {
  name                    = "ai-departments-vpc"
  auto_create_subnetworks = false
}

resource "google_compute_subnetwork" "main" {
  name          = "ai-departments-subnet"
  ip_cidr_range = "10.0.0.0/16"
  region        = var.region
  network       = google_compute_network.main.id
  
  secondary_ip_range {
    range_name    = "services-range"
    ip_cidr_range = "192.168.1.0/24"
  }
}

resource "google_vpc_access_connector" "main" {
  name          = "ai-departments-connector"
  region        = var.region
  ip_cidr_range = "10.8.0.0/28"
  network       = google_compute_network.main.name
  
  min_throughput = 200
  max_throughput = 1000
}

# Load Balancer
resource "google_compute_global_address" "main" {
  name = "ai-departments-ip"
}

resource "google_compute_managed_ssl_certificate" "main" {
  name = "ai-departments-ssl"
  
  managed {
    domains = [
      "aidepartments.com.br",
      "app.aidepartments.com.br",
      "api.aidepartments.com.br"
    ]
  }
}

# Cloud Storage Buckets
resource "google_storage_bucket" "assets" {
  name     = "ai-departments-assets"
  location = "US"
  
  cors {
    origin          = ["https://app.aidepartments.com.br"]
    method          = ["GET", "HEAD", "PUT", "POST", "DELETE"]
    response_header = ["*"]
    max_age_seconds = 3600
  }
  
  lifecycle_rule {
    action {
      type = "Delete"
    }
    condition {
      age = 365
    }
  }
}

# Monitoring and Alerting
module "monitoring" {
  source = "../../modules/monitoring"
  
  project_id = var.project_id
  
  notification_channels = [
    google_monitoring_notification_channel.email.name,
    google_monitoring_notification_channel.slack.name
  ]
}
```

---

## Docker Configuration

### Frontend Dockerfile
```dockerfile
# frontend/Dockerfile
FROM node:18-alpine AS base

# Dependencies
FROM base AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --only=production

# Builder
FROM base AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npm run build

# Runner
FROM base AS runner
WORKDIR /app

ENV NODE_ENV=production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public

# Automatically leverage output traces to reduce image size
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000
ENV PORT 3000

CMD ["node", "server.js"]
```

### Backend Dockerfile
```dockerfile
# backend/Dockerfile
FROM python:3.11-slim AS base

# Install system dependencies
RUN apt-get update && apt-get install -y \
    postgresql-client \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Install Python dependencies
FROM base AS deps
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
FROM deps AS runner
COPY app/ ./app/
COPY alembic.ini ./
COPY migrations/ ./migrations/

# Create non-root user
RUN useradd --create-home --shell /bin/bash app
RUN chown -R app:app /app
USER app

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8000/health || exit 1

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## Deployment Scripts

### Database Migration Script
```bash
#!/bin/bash
# scripts/migrate-production.sh

set -e

echo "🔄 Starting production database migration..."

# Backup current database
echo "📦 Creating database backup..."
gcloud sql export sql ai-departments-prod-db \
  gs://ai-departments-backups/pre-migration-$(date +%Y%m%d%H%M%S).sql \
  --database=ai_departments

# Test migration on replica
echo "🧪 Testing migration on read replica..."
gcloud sql instances create ai-departments-migration-test \
  --master-instance-name=ai-departments-prod-db \
  --replica-type=READ

# Wait for replica to be ready
sleep 60

# Apply migration to test replica
export DATABASE_URL="postgresql://user:pass@replica-ip/ai_departments"
alembic upgrade head

# If test succeeds, apply to production
echo "✅ Test migration successful, applying to production..."

# Put application in maintenance mode
gcloud run services replace-traffic frontend \
  --to-tags=maintenance:100 \
  --region=us-central1

# Apply migration to production
export DATABASE_URL="$PROD_DATABASE_URL"
alembic upgrade head

echo "✅ Production migration completed successfully"

# Cleanup test replica
gcloud sql instances delete ai-departments-migration-test --quiet
```

### Production Verification Script
```bash
#!/bin/bash
# scripts/verify-production.sh

set -e

echo "🔍 Verifying production deployment..."

BASE_URL="https://api.aidepartments.com.br"
FRONTEND_URL="https://app.aidepartments.com.br"

# Health check endpoints
endpoints=(
  "$BASE_URL/health"
  "$BASE_URL/health/db"
  "$BASE_URL/health/redis"
  "$FRONTEND_URL/api/health"
)

for endpoint in "${endpoints[@]}"; do
  echo "Checking $endpoint..."
  
  response=$(curl -s -o /dev/null -w "%{http_code}" "$endpoint")
  
  if [ "$response" != "200" ]; then
    echo "❌ Health check failed for $endpoint (HTTP $response)"
    exit 1
  fi
  
  echo "✅ $endpoint is healthy"
done

# Test critical user flows
echo "🧪 Testing critical user flows..."

# Test user registration
curl -X POST "$BASE_URL/auth/register" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test-prod-'$(date +%s)'@example.com",
    "password": "test123456",
    "full_name": "Test User",
    "business_name": "Test Business"
  }' \
  --fail --silent --output /dev/null

echo "✅ User registration flow working"

# Test WhatsApp webhook endpoint
curl -X GET "$BASE_URL/webhooks/whatsapp/test" \
  -H "Authorization: Bearer test-token" \
  --fail --silent --output /dev/null

echo "✅ WhatsApp webhook endpoint accessible"

# Test database connectivity
curl -X GET "$BASE_URL/health/db" --fail --silent --output /dev/null
echo "✅ Database connectivity verified"

# Test Redis connectivity
curl -X GET "$BASE_URL/health/redis" --fail --silent --output /dev/null
echo "✅ Redis connectivity verified"

echo "🎉 All production verification checks passed!"
```

---

## Monitoring & Alerting

### Production Monitoring Setup
```yaml
# k8s/monitoring/prometheus-config.yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      evaluation_interval: 15s
    
    rule_files:
      - "alert_rules.yml"
    
    alertmanager:
      alertmanagers:
        - static_configs:
            - targets: ['alertmanager:9093']
    
    scrape_configs:
      - job_name: 'ai-departments-backend'
        metrics_path: '/metrics'
        static_configs:
          - targets: ['backend:8000']
        
      - job_name: 'ai-departments-frontend'
        metrics_path: '/api/metrics'
        static_configs:
          - targets: ['frontend:3000']
        
      - job_name: 'postgres-exporter'
        static_configs:
          - targets: ['postgres-exporter:9187']
        
      - job_name: 'redis-exporter'
        static_configs:
          - targets: ['redis-exporter:9121']

  alert_rules.yml: |
    groups:
      - name: ai-departments-alerts
        rules:
          - alert: HighErrorRate
            expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.1
            for: 2m
            labels:
              severity: critical
            annotations:
              summary: "High error rate detected"
              description: "Error rate is {{ $value }} requests/second"
              
          - alert: DatabaseConnectionsHigh
            expr: postgres_stat_activity_count > 150
            for: 5m
            labels:
              severity: warning
            annotations:
              summary: "High database connection count"
              
          - alert: APIResponseTimeSlow
            expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 2
            for: 10m
            labels:
              severity: warning
            annotations:
              summary: "API response time is slow"
```

### Grafana Dashboard
```json
{
  "dashboard": {
    "id": null,
    "title": "AI Departments Platform",
    "description": "Production monitoring dashboard",
    "tags": ["ai-departments", "production"],
    "timezone": "America/Sao_Paulo",
    "panels": [
      {
        "title": "Request Rate",
        "type": "graph",
        "targets": [
          {
            "expr": "rate(http_requests_total[5m])",
            "legendFormat": "{{method}} {{route}}"
          }
        ]
      },
      {
        "title": "Error Rate",
        "type": "singlestat",
        "targets": [
          {
            "expr": "rate(http_requests_total{status=~\"5..\"}[5m]) / rate(http_requests_total[5m])",
            "legendFormat": "Error Rate"
          }
        ],
        "thresholds": [
          {"color": "green", "value": 0},
          {"color": "yellow", "value": 0.01},
          {"color": "red", "value": 0.05}
        ]
      },
      {
        "title": "Database Performance",
        "type": "graph",
        "targets": [
          {
            "expr": "postgres_stat_database_tup_fetched_rate",
            "legendFormat": "Rows fetched/sec"
          },
          {
            "expr": "postgres_stat_database_tup_inserted_rate",
            "legendFormat": "Rows inserted/sec"
          }
        ]
      },
      {
        "title": "AI Model Usage",
        "type": "graph",
        "targets": [
          {
            "expr": "rate(ai_model_requests_total[5m])",
            "legendFormat": "{{model}} requests/sec"
          }
        ]
      }
    ]
  }
}
```

---

## Security & Compliance

### Security Scanning Pipeline
```yaml
# .github/workflows/security-scan.yml
name: Security Scanning

on:
  schedule:
    - cron: '0 2 * * *'  # Daily at 2 AM
  workflow_dispatch:

jobs:
  container-security:
    runs-on: ubuntu-latest
    steps:
      - name: Scan production images
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'image'
          image-ref: 'gcr.io/ai-departments-prod/backend:latest'
          format: 'sarif'
          output: 'trivy-results.sarif'
          
      - name: Upload scan results
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'

  infrastructure-security:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Run Checkov
        uses: bridgecrewio/checkov-action@master
        with:
          directory: infrastructure/
          framework: terraform
          
      - name: Security baseline check
        run: |
          # Check for security misconfigurations
          ./scripts/security-baseline-check.sh
```

### Disaster Recovery Procedures
```bash
#!/bin/bash
# scripts/disaster-recovery.sh

set -e

echo "🚨 Initiating disaster recovery procedures..."

# Step 1: Assess the situation
echo "📊 Assessing current system state..."
./scripts/health-check-all.sh || echo "⚠️ System health check failed"

# Step 2: Switch to DR region
echo "🔄 Switching to disaster recovery region..."
gcloud config set compute/region us-east1

# Step 3: Restore from latest backup
echo "💾 Restoring database from latest backup..."
LATEST_BACKUP=$(gcloud sql backups list \
  --instance=ai-departments-prod-db \
  --format="value(id)" \
  --limit=1)

gcloud sql backups restore $LATEST_BACKUP \
  --restore-instance=ai-departments-dr-db

# Step 4: Update DNS to point to DR environment
echo "🌐 Updating DNS records..."
gcloud dns record-sets transaction start --zone=aidepartments-zone
gcloud dns record-sets transaction remove \
  --name=api.aidepartments.com.br. \
  --ttl=300 \
  --type=A \
  --zone=aidepartments-zone \
  "$(gcloud dns record-sets list --zone=aidepartments-zone --name=api.aidepartments.com.br. --format='value(rrdatas[0])')"

gcloud dns record-sets transaction add \
  --name=api.aidepartments.com.br. \
  --ttl=60 \
  --type=A \
  --zone=aidepartments-zone \
  "$DR_LOAD_BALANCER_IP"

gcloud dns record-sets transaction execute --zone=aidepartments-zone

echo "✅ Disaster recovery completed. System running on DR infrastructure."
echo "🔔 Alert teams and customers about the incident."
```

---

**Document Status:** Comprehensive CI/CD pipeline specification complete with security, monitoring, and disaster recovery procedures. Ready for implementation and production deployment.