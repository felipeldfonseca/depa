# Database Schema Design
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Claude (Tech Lead)  
**Database:** PostgreSQL 15 + pgvector  
**Status:** APPROVED  

---

## Schema Overview

### Design Principles
- **Multi-tenant Architecture**: Organization-level data isolation with shared infrastructure
- **Audit Trail**: Complete history of all business-critical operations
- **Scalability**: Optimized for 10K+ organizations with millions of records
- **Security**: Row-level security and encryption for sensitive data
- **Performance**: Strategic indexing and partitioning for sub-second queries

### Database Structure
```sql
-- Core Schemas
CREATE SCHEMA IF NOT EXISTS platform;      -- Core platform entities
CREATE SCHEMA IF NOT EXISTS departments;   -- Department configurations and state
CREATE SCHEMA IF NOT EXISTS agents;        -- AI agent data and workflows  
CREATE SCHEMA IF NOT EXISTS integrations; -- External service connections
CREATE SCHEMA IF NOT EXISTS analytics;     -- Metrics and reporting data
CREATE SCHEMA IF NOT EXISTS audit;         -- Audit logs and compliance
```

---

## Core Platform Schema

### Organizations (Tenants)
```sql
CREATE TABLE platform.organizations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    
    -- Business Information
    business_type VARCHAR(50) NOT NULL, -- 'fashion', 'food', 'services', etc.
    industry_vertical VARCHAR(50),
    target_market VARCHAR(100),
    
    -- Configuration
    timezone VARCHAR(50) DEFAULT 'America/Sao_Paulo',
    locale VARCHAR(10) DEFAULT 'pt-BR',
    currency VARCHAR(3) DEFAULT 'BRL',
    
    -- Billing
    subscription_plan VARCHAR(20) DEFAULT 'starter', -- 'starter', 'growth', 'scale'
    billing_status VARCHAR(20) DEFAULT 'trial', -- 'trial', 'active', 'suspended', 'cancelled'
    trial_ends_at TIMESTAMP WITH TIME ZONE,
    
    -- Security
    encryption_key_id VARCHAR(255), -- Reference to encryption key
    
    -- Metadata
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    deleted_at TIMESTAMP WITH TIME ZONE, -- Soft delete
    
    -- Search
    search_vector tsvector GENERATED ALWAYS AS (to_tsvector('portuguese', name || ' ' || COALESCE(business_type, ''))) STORED
);

-- Indexes
CREATE INDEX idx_organizations_slug ON platform.organizations(slug);
CREATE INDEX idx_organizations_business_type ON platform.organizations(business_type);
CREATE INDEX idx_organizations_subscription ON platform.organizations(subscription_plan, billing_status);
CREATE INDEX idx_organizations_search ON platform.organizations USING GIN(search_vector);
```

### Users & Authentication
```sql
CREATE TABLE platform.users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    
    -- Identity
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20),
    full_name VARCHAR(255) NOT NULL,
    avatar_url VARCHAR(500),
    
    -- Authentication
    password_hash VARCHAR(255), -- bcrypt hash
    email_verified_at TIMESTAMP WITH TIME ZONE,
    phone_verified_at TIMESTAMP WITH TIME ZONE,
    two_factor_enabled BOOLEAN DEFAULT FALSE,
    two_factor_secret VARCHAR(255),
    
    -- Authorization
    role VARCHAR(20) DEFAULT 'member', -- 'owner', 'admin', 'member'
    permissions JSONB DEFAULT '[]'::JSONB,
    
    -- Session Management
    last_login_at TIMESTAMP WITH TIME ZONE,
    login_count INTEGER DEFAULT 0,
    
    -- Preferences
    notification_preferences JSONB DEFAULT '{
        "email": {"marketing": true, "alerts": true, "reports": true},
        "sms": {"alerts": true, "critical": true},
        "push": {"real_time": true, "daily_summary": false}
    }'::JSONB,
    
    -- Metadata
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    deleted_at TIMESTAMP WITH TIME ZONE
);

-- Indexes
CREATE INDEX idx_users_organization ON platform.users(organization_id);
CREATE INDEX idx_users_email ON platform.users(email) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_role ON platform.users(organization_id, role);
```

### User Sessions
```sql
CREATE TABLE platform.user_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES platform.users(id) ON DELETE CASCADE,
    
    -- Session Data
    refresh_token_hash VARCHAR(255) NOT NULL,
    access_token_jti VARCHAR(255) NOT NULL, -- JWT ID for revocation
    device_info JSONB,
    ip_address INET,
    user_agent TEXT,
    
    -- Lifecycle
    expires_at TIMESTAMP WITH TIME ZONE NOT NULL,
    revoked_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_sessions_user ON platform.user_sessions(user_id);
CREATE INDEX idx_sessions_token ON platform.user_sessions(refresh_token_hash);
CREATE INDEX idx_sessions_jti ON platform.user_sessions(access_token_jti);
CREATE INDEX idx_sessions_expires ON platform.user_sessions(expires_at);
```

---

## Departments Schema

### Department Configurations
```sql
CREATE TABLE departments.departments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    
    -- Department Info
    type VARCHAR(50) NOT NULL, -- 'marketing', 'customer_service', 'design', etc.
    name VARCHAR(255) NOT NULL,
    description TEXT,
    
    -- Configuration
    configuration JSONB NOT NULL DEFAULT '{}'::JSONB,
    industry_template VARCHAR(50), -- 'fashion', 'food', 'services'
    
    -- State Management
    status VARCHAR(20) DEFAULT 'active', -- 'active', 'paused', 'archived'
    last_activity_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Performance Tracking
    metrics JSONB DEFAULT '{
        "total_tasks": 0,
        "successful_tasks": 0,
        "failed_tasks": 0,
        "avg_response_time": 0
    }'::JSONB,
    
    -- Metadata
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    UNIQUE(organization_id, type)
);

-- Indexes
CREATE INDEX idx_departments_org ON departments.departments(organization_id);
CREATE INDEX idx_departments_type ON departments.departments(type);
CREATE INDEX idx_departments_status ON departments.departments(organization_id, status);
CREATE INDEX idx_departments_activity ON departments.departments(last_activity_at);
```

### Agent Instances
```sql
CREATE TABLE departments.agents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    department_id UUID NOT NULL REFERENCES departments.departments(id) ON DELETE CASCADE,
    
    -- Agent Definition
    agent_type VARCHAR(50) NOT NULL, -- 'social_media', 'whatsapp_responder', 'faq_builder'
    name VARCHAR(255) NOT NULL,
    description TEXT,
    
    -- Configuration
    configuration JSONB NOT NULL DEFAULT '{}'::JSONB,
    model_configuration JSONB DEFAULT '{
        "primary_model": "gpt-4",
        "fallback_model": "gpt-3.5-turbo",
        "temperature": 0.7,
        "max_tokens": 1000
    }'::JSONB,
    
    -- State
    status VARCHAR(20) DEFAULT 'active', -- 'active', 'paused', 'error', 'training'
    last_execution_at TIMESTAMP WITH TIME ZONE,
    next_scheduled_at TIMESTAMP WITH TIME ZONE,
    
    -- Performance
    execution_count INTEGER DEFAULT 0,
    success_count INTEGER DEFAULT 0,
    avg_execution_time_ms INTEGER DEFAULT 0,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_agents_department ON departments.agents(department_id);
CREATE INDEX idx_agents_type ON departments.agents(agent_type);
CREATE INDEX idx_agents_status ON departments.agents(status);
CREATE INDEX idx_agents_schedule ON departments.agents(next_scheduled_at) WHERE status = 'active';
```

### Agent Executions & Workflows
```sql
CREATE TABLE departments.agent_executions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    agent_id UUID NOT NULL REFERENCES departments.agents(id) ON DELETE CASCADE,
    
    -- Execution Context
    trigger_type VARCHAR(50) NOT NULL, -- 'scheduled', 'webhook', 'manual', 'event'
    trigger_data JSONB,
    
    -- Execution Details
    status VARCHAR(20) DEFAULT 'pending', -- 'pending', 'running', 'completed', 'failed'
    started_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    completed_at TIMESTAMP WITH TIME ZONE,
    execution_time_ms INTEGER,
    
    -- Input/Output
    input_data JSONB,
    output_data JSONB,
    error_details JSONB,
    
    -- AI Model Usage
    model_used VARCHAR(50),
    tokens_consumed INTEGER DEFAULT 0,
    cost_usd DECIMAL(10,6) DEFAULT 0,
    
    -- Metadata
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_executions_agent ON departments.agent_executions(agent_id);
CREATE INDEX idx_executions_status ON departments.agent_executions(status);
CREATE INDEX idx_executions_created ON departments.agent_executions(created_at);
CREATE INDEX idx_executions_trigger ON departments.agent_executions(trigger_type, created_at);

-- Partitioning by month for performance
CREATE TABLE departments.agent_executions_y2025m01 PARTITION OF departments.agent_executions
    FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');
-- Additional partitions created automatically
```

---

## Content & Assets Schema

### Generated Content
```sql
CREATE TABLE platform.content (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    agent_execution_id UUID REFERENCES departments.agent_executions(id),
    
    -- Content Classification
    content_type VARCHAR(50) NOT NULL, -- 'social_post', 'whatsapp_response', 'email', 'blog_post'
    platform VARCHAR(30), -- 'instagram', 'facebook', 'whatsapp', 'email'
    
    -- Content Data
    title VARCHAR(500),
    body TEXT NOT NULL,
    media_urls JSONB DEFAULT '[]'::JSONB,
    metadata JSONB DEFAULT '{}'::JSONB,
    
    -- Scheduling & Publishing
    scheduled_for TIMESTAMP WITH TIME ZONE,
    published_at TIMESTAMP WITH TIME ZONE,
    status VARCHAR(20) DEFAULT 'draft', -- 'draft', 'scheduled', 'published', 'failed'
    
    -- Engagement Tracking
    engagement_metrics JSONB DEFAULT '{
        "views": 0,
        "likes": 0,
        "comments": 0,
        "shares": 0,
        "clicks": 0
    }'::JSONB,
    
    -- Quality Control
    human_reviewed BOOLEAN DEFAULT FALSE,
    human_approved BOOLEAN DEFAULT FALSE,
    review_notes TEXT,
    
    -- Content Analysis
    sentiment_score DECIMAL(3,2), -- -1.0 to 1.0
    readability_score INTEGER, -- 0-100
    brand_alignment_score DECIMAL(3,2), -- 0-1.0
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_content_org ON platform.content(organization_id);
CREATE INDEX idx_content_type ON platform.content(content_type);
CREATE INDEX idx_content_platform ON platform.content(platform);
CREATE INDEX idx_content_status ON platform.content(status);
CREATE INDEX idx_content_scheduled ON platform.content(scheduled_for) WHERE status = 'scheduled';
CREATE INDEX idx_content_published ON platform.content(published_at) WHERE published_at IS NOT NULL;
```

### Media Assets
```sql
CREATE TABLE platform.media_assets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    
    -- Asset Information
    filename VARCHAR(255) NOT NULL,
    original_filename VARCHAR(255),
    mime_type VARCHAR(100) NOT NULL,
    file_size_bytes BIGINT NOT NULL,
    
    -- Storage
    storage_provider VARCHAR(20) DEFAULT 'gcs', -- 'gcs', 's3', 'local'
    storage_path VARCHAR(500) NOT NULL,
    public_url VARCHAR(500),
    
    -- Image-specific Data
    image_metadata JSONB, -- width, height, format, etc.
    alt_text VARCHAR(500),
    
    -- AI Generation Data
    generation_prompt TEXT,
    generation_model VARCHAR(50),
    generation_cost DECIMAL(8,4),
    
    -- Usage Tracking
    usage_count INTEGER DEFAULT 0,
    last_used_at TIMESTAMP WITH TIME ZONE,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_media_org ON platform.media_assets(organization_id);
CREATE INDEX idx_media_type ON platform.media_assets(mime_type);
CREATE INDEX idx_media_storage ON platform.media_assets(storage_provider, storage_path);
CREATE INDEX idx_media_usage ON platform.media_assets(usage_count DESC, last_used_at DESC);
```

---

## Integrations Schema

### External Connections
```sql
CREATE TABLE integrations.connections (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    
    -- Integration Details
    integration_type VARCHAR(50) NOT NULL, -- 'whatsapp', 'instagram', 'facebook', 'stripe'
    provider VARCHAR(50) NOT NULL,
    account_identifier VARCHAR(255), -- phone number, page ID, etc.
    
    -- Authentication
    access_token TEXT, -- Encrypted
    refresh_token TEXT, -- Encrypted
    token_expires_at TIMESTAMP WITH TIME ZONE,
    
    -- Configuration
    configuration JSONB DEFAULT '{}'::JSONB,
    webhook_url VARCHAR(500),
    webhook_secret VARCHAR(255),
    
    -- Status
    status VARCHAR(20) DEFAULT 'active', -- 'active', 'expired', 'revoked', 'error'
    last_sync_at TIMESTAMP WITH TIME ZONE,
    last_error TEXT,
    
    -- Rate Limiting
    rate_limit_per_hour INTEGER,
    requests_this_hour INTEGER DEFAULT 0,
    rate_limit_reset_at TIMESTAMP WITH TIME ZONE,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    UNIQUE(organization_id, integration_type, account_identifier)
);

-- Indexes
CREATE INDEX idx_connections_org ON integrations.connections(organization_id);
CREATE INDEX idx_connections_type ON integrations.connections(integration_type);
CREATE INDEX idx_connections_status ON integrations.connections(status);
CREATE INDEX idx_connections_sync ON integrations.connections(last_sync_at);
```

### Webhook Events
```sql
CREATE TABLE integrations.webhook_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    connection_id UUID NOT NULL REFERENCES integrations.connections(id) ON DELETE CASCADE,
    
    -- Event Details
    event_type VARCHAR(100) NOT NULL,
    event_id VARCHAR(255), -- External event ID
    
    -- Payload
    headers JSONB,
    payload JSONB NOT NULL,
    signature VARCHAR(255),
    
    -- Processing
    status VARCHAR(20) DEFAULT 'pending', -- 'pending', 'processing', 'processed', 'failed'
    processed_at TIMESTAMP WITH TIME ZONE,
    error_message TEXT,
    retry_count INTEGER DEFAULT 0,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_webhook_events_connection ON integrations.webhook_events(connection_id);
CREATE INDEX idx_webhook_events_status ON integrations.webhook_events(status);
CREATE INDEX idx_webhook_events_type ON integrations.webhook_events(event_type);
CREATE INDEX idx_webhook_events_created ON integrations.webhook_events(created_at);
```

---

## Customer Interactions Schema

### Conversations
```sql
CREATE TABLE platform.conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    
    -- Conversation Details
    channel VARCHAR(30) NOT NULL, -- 'whatsapp', 'instagram', 'facebook', 'email'
    external_conversation_id VARCHAR(255), -- Platform-specific ID
    
    -- Participants
    customer_phone VARCHAR(20),
    customer_email VARCHAR(255),
    customer_name VARCHAR(255),
    customer_external_id VARCHAR(255), -- Platform user ID
    
    -- Status
    status VARCHAR(20) DEFAULT 'active', -- 'active', 'resolved', 'escalated', 'archived'
    priority VARCHAR(10) DEFAULT 'normal', -- 'low', 'normal', 'high', 'urgent'
    
    -- Assignment
    assigned_to_human BOOLEAN DEFAULT FALSE,
    assigned_user_id UUID REFERENCES platform.users(id),
    escalated_at TIMESTAMP WITH TIME ZONE,
    resolved_at TIMESTAMP WITH TIME ZONE,
    
    -- Metrics
    first_response_time_seconds INTEGER,
    resolution_time_seconds INTEGER,
    message_count INTEGER DEFAULT 0,
    human_takeover_count INTEGER DEFAULT 0,
    
    -- Metadata
    tags JSONB DEFAULT '[]'::JSONB,
    summary TEXT,
    satisfaction_score INTEGER, -- 1-5 rating
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_conversations_org ON platform.conversations(organization_id);
CREATE INDEX idx_conversations_channel ON platform.conversations(channel);
CREATE INDEX idx_conversations_status ON platform.conversations(status);
CREATE INDEX idx_conversations_customer_phone ON platform.conversations(customer_phone);
CREATE INDEX idx_conversations_assigned ON platform.conversations(assigned_to_human, assigned_user_id);
CREATE INDEX idx_conversations_created ON platform.conversations(created_at);
```

### Messages
```sql
CREATE TABLE platform.messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    conversation_id UUID NOT NULL REFERENCES platform.conversations(id) ON DELETE CASCADE,
    
    -- Message Details
    direction VARCHAR(10) NOT NULL, -- 'inbound', 'outbound'
    message_type VARCHAR(20) DEFAULT 'text', -- 'text', 'image', 'audio', 'video', 'document'
    
    -- Content
    text_content TEXT,
    media_urls JSONB DEFAULT '[]'::JSONB,
    metadata JSONB DEFAULT '{}'::JSONB,
    
    -- Source
    sender_type VARCHAR(10) NOT NULL, -- 'customer', 'ai', 'human'
    sender_user_id UUID REFERENCES platform.users(id),
    agent_id UUID REFERENCES departments.agents(id),
    
    -- External Platform Data
    external_message_id VARCHAR(255),
    platform_timestamp TIMESTAMP WITH TIME ZONE,
    
    -- AI Processing
    intent_classification VARCHAR(100),
    sentiment_score DECIMAL(3,2), -- -1.0 to 1.0
    confidence_score DECIMAL(3,2), -- 0-1.0
    entities_extracted JSONB DEFAULT '[]'::JSONB,
    
    -- Response Generation (for AI messages)
    prompt_used TEXT,
    model_used VARCHAR(50),
    generation_time_ms INTEGER,
    tokens_used INTEGER,
    
    -- Delivery Status
    delivery_status VARCHAR(20) DEFAULT 'pending', -- 'pending', 'sent', 'delivered', 'read', 'failed'
    delivered_at TIMESTAMP WITH TIME ZONE,
    read_at TIMESTAMP WITH TIME ZONE,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_messages_conversation ON platform.messages(conversation_id, created_at);
CREATE INDEX idx_messages_sender ON platform.messages(sender_type, sender_user_id);
CREATE INDEX idx_messages_agent ON platform.messages(agent_id) WHERE agent_id IS NOT NULL;
CREATE INDEX idx_messages_intent ON platform.messages(intent_classification);
CREATE INDEX idx_messages_sentiment ON platform.messages(sentiment_score) WHERE sentiment_score IS NOT NULL;
```

---

## Knowledge Base Schema

### Company Knowledge
```sql
CREATE TABLE platform.knowledge_base (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    
    -- Content
    title VARCHAR(500) NOT NULL,
    content TEXT NOT NULL,
    content_type VARCHAR(30) DEFAULT 'faq', -- 'faq', 'policy', 'procedure', 'product_info'
    
    -- Classification
    category VARCHAR(100),
    tags JSONB DEFAULT '[]'::JSONB,
    keywords JSONB DEFAULT '[]'::JSONB,
    
    -- AI Enhancement
    embedding VECTOR(1536), -- OpenAI embedding dimension
    summary TEXT,
    auto_generated BOOLEAN DEFAULT FALSE,
    
    -- Usage Stats
    access_count INTEGER DEFAULT 0,
    last_accessed_at TIMESTAMP WITH TIME ZONE,
    effectiveness_score DECIMAL(3,2) DEFAULT 0.5, -- 0-1.0
    
    -- Versioning
    version INTEGER DEFAULT 1,
    parent_id UUID REFERENCES platform.knowledge_base(id),
    
    -- Lifecycle
    status VARCHAR(20) DEFAULT 'active', -- 'active', 'deprecated', 'archived'
    reviewed_at TIMESTAMP WITH TIME ZONE,
    reviewed_by UUID REFERENCES platform.users(id),
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_knowledge_org ON platform.knowledge_base(organization_id);
CREATE INDEX idx_knowledge_type ON platform.knowledge_base(content_type);
CREATE INDEX idx_knowledge_category ON platform.knowledge_base(category);
CREATE INDEX idx_knowledge_status ON platform.knowledge_base(status);
CREATE INDEX idx_knowledge_embedding ON platform.knowledge_base USING ivfflat (embedding vector_cosine_ops);
CREATE INDEX idx_knowledge_search ON platform.knowledge_base USING GIN(to_tsvector('portuguese', title || ' ' || content));
```

---

## Analytics Schema

### Usage Metrics
```sql
CREATE TABLE analytics.usage_metrics (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    
    -- Metric Definition
    metric_type VARCHAR(50) NOT NULL, -- 'message_count', 'content_generated', 'response_time'
    metric_category VARCHAR(30) NOT NULL, -- 'engagement', 'performance', 'usage', 'cost'
    
    -- Values
    metric_value DECIMAL(15,4) NOT NULL,
    metric_unit VARCHAR(20), -- 'count', 'seconds', 'bytes', 'usd'
    
    -- Dimensions
    dimensions JSONB DEFAULT '{}'::JSONB, -- department_type, agent_type, etc.
    
    -- Time Period
    period_start TIMESTAMP WITH TIME ZONE NOT NULL,
    period_end TIMESTAMP WITH TIME ZONE NOT NULL,
    granularity VARCHAR(10) NOT NULL, -- 'hour', 'day', 'week', 'month'
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
) PARTITION BY RANGE (period_start);

-- Create partitions
CREATE TABLE analytics.usage_metrics_2025_01 PARTITION OF analytics.usage_metrics
    FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');

-- Indexes
CREATE INDEX idx_usage_metrics_org ON analytics.usage_metrics(organization_id);
CREATE INDEX idx_usage_metrics_type ON analytics.usage_metrics(metric_type, metric_category);
CREATE INDEX idx_usage_metrics_period ON analytics.usage_metrics(period_start, period_end);
CREATE INDEX idx_usage_metrics_dims ON analytics.usage_metrics USING GIN(dimensions);
```

### Customer Satisfaction
```sql
CREATE TABLE analytics.satisfaction_scores (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    conversation_id UUID REFERENCES platform.conversations(id),
    
    -- Score Details
    score INTEGER CHECK (score >= 1 AND score <= 5),
    feedback_text TEXT,
    
    -- Context
    interaction_type VARCHAR(30), -- 'whatsapp_conversation', 'social_post_engagement'
    department_type VARCHAR(30),
    agent_type VARCHAR(50),
    
    -- Customer Info (anonymized)
    customer_segment VARCHAR(30),
    customer_tenure_days INTEGER,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_satisfaction_org ON analytics.satisfaction_scores(organization_id);
CREATE INDEX idx_satisfaction_score ON analytics.satisfaction_scores(score);
CREATE INDEX idx_satisfaction_type ON analytics.satisfaction_scores(interaction_type);
CREATE INDEX idx_satisfaction_created ON analytics.satisfaction_scores(created_at);
```

---

## Billing Schema

### Subscription Management
```sql
CREATE TABLE platform.subscriptions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    
    -- Plan Details
    plan_name VARCHAR(50) NOT NULL, -- 'starter', 'growth', 'scale'
    plan_features JSONB NOT NULL,
    
    -- Billing
    billing_cycle VARCHAR(20) DEFAULT 'monthly', -- 'monthly', 'annual'
    amount_cents INTEGER NOT NULL,
    currency VARCHAR(3) DEFAULT 'BRL',
    
    -- External References
    stripe_subscription_id VARCHAR(255),
    mercadopago_subscription_id VARCHAR(255),
    
    -- Lifecycle
    status VARCHAR(20) DEFAULT 'active', -- 'trialing', 'active', 'past_due', 'canceled'
    current_period_start TIMESTAMP WITH TIME ZONE NOT NULL,
    current_period_end TIMESTAMP WITH TIME ZONE NOT NULL,
    trial_end TIMESTAMP WITH TIME ZONE,
    canceled_at TIMESTAMP WITH TIME ZONE,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_subscriptions_org ON platform.subscriptions(organization_id);
CREATE INDEX idx_subscriptions_status ON platform.subscriptions(status);
CREATE INDEX idx_subscriptions_period ON platform.subscriptions(current_period_end);
```

### Usage Tracking (for billing)
```sql
CREATE TABLE platform.usage_records (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES platform.organizations(id) ON DELETE CASCADE,
    
    -- Usage Details
    usage_type VARCHAR(50) NOT NULL, -- 'ai_tokens', 'messages_sent', 'storage_gb'
    quantity DECIMAL(15,4) NOT NULL,
    unit_cost_cents INTEGER,
    
    -- Billing Period
    billing_period_start TIMESTAMP WITH TIME ZONE NOT NULL,
    billing_period_end TIMESTAMP WITH TIME ZONE NOT NULL,
    
    -- Source Tracking
    department_type VARCHAR(30),
    agent_type VARCHAR(50),
    
    recorded_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
) PARTITION BY RANGE (billing_period_start);

-- Indexes
CREATE INDEX idx_usage_records_org ON platform.usage_records(organization_id);
CREATE INDEX idx_usage_records_type ON platform.usage_records(usage_type);
CREATE INDEX idx_usage_records_period ON platform.usage_records(billing_period_start, billing_period_end);
```

---

## Security & Audit Schema

### Audit Logs
```sql
CREATE TABLE audit.activity_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID REFERENCES platform.organizations(id),
    user_id UUID REFERENCES platform.users(id),
    
    -- Activity Details
    action VARCHAR(100) NOT NULL, -- 'user.login', 'department.created', 'message.sent'
    resource_type VARCHAR(50), -- 'user', 'department', 'message', 'integration'
    resource_id UUID,
    
    -- Context
    ip_address INET,
    user_agent TEXT,
    request_id UUID,
    
    -- Data
    old_values JSONB,
    new_values JSONB,
    metadata JSONB DEFAULT '{}'::JSONB,
    
    -- Result
    success BOOLEAN DEFAULT TRUE,
    error_message TEXT,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
) PARTITION BY RANGE (created_at);

-- Create monthly partitions
CREATE TABLE audit.activity_logs_2025_01 PARTITION OF audit.activity_logs
    FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');

-- Indexes
CREATE INDEX idx_activity_logs_org ON audit.activity_logs(organization_id);
CREATE INDEX idx_activity_logs_user ON audit.activity_logs(user_id);
CREATE INDEX idx_activity_logs_action ON audit.activity_logs(action);
CREATE INDEX idx_activity_logs_resource ON audit.activity_logs(resource_type, resource_id);
CREATE INDEX idx_activity_logs_created ON audit.activity_logs(created_at);
```

---

## Multi-Tenancy & Security

### Row Level Security
```sql
-- Enable RLS on all tenant-scoped tables
ALTER TABLE platform.users ENABLE ROW LEVEL SECURITY;
ALTER TABLE departments.departments ENABLE ROW LEVEL SECURITY;
ALTER TABLE departments.agents ENABLE ROW LEVEL SECURITY;
ALTER TABLE platform.content ENABLE ROW LEVEL SECURITY;
ALTER TABLE platform.conversations ENABLE ROW LEVEL SECURITY;
ALTER TABLE platform.messages ENABLE ROW LEVEL SECURITY;

-- Create policies for tenant isolation
CREATE POLICY tenant_isolation_users ON platform.users
    FOR ALL TO application_role
    USING (organization_id = current_setting('app.current_organization_id')::UUID);

CREATE POLICY tenant_isolation_departments ON departments.departments
    FOR ALL TO application_role
    USING (organization_id = current_setting('app.current_organization_id')::UUID);

-- Similar policies for other tables...
```

### Encryption Functions
```sql
-- Function to encrypt sensitive data
CREATE OR REPLACE FUNCTION encrypt_sensitive_data(
    data TEXT,
    organization_id UUID
) RETURNS TEXT AS $$
BEGIN
    -- Use organization-specific encryption key
    RETURN pgp_sym_encrypt(data, 
        get_organization_encryption_key(organization_id),
        'compress-algo=1, cipher-algo=aes256'
    );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Function to decrypt sensitive data
CREATE OR REPLACE FUNCTION decrypt_sensitive_data(
    encrypted_data TEXT,
    organization_id UUID
) RETURNS TEXT AS $$
BEGIN
    RETURN pgp_sym_decrypt(encrypted_data,
        get_organization_encryption_key(organization_id)
    );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## Performance Optimizations

### Partitioning Strategy
```sql
-- Automatic partition creation for time-series data
CREATE OR REPLACE FUNCTION create_monthly_partition(
    table_name TEXT,
    start_date DATE
) RETURNS VOID AS $$
DECLARE
    partition_name TEXT;
    end_date DATE;
BEGIN
    partition_name := table_name || '_' || to_char(start_date, 'YYYY_MM');
    end_date := start_date + INTERVAL '1 month';
    
    EXECUTE format('CREATE TABLE IF NOT EXISTS %I PARTITION OF %I 
                    FOR VALUES FROM (%L) TO (%L)',
                   partition_name, table_name, start_date, end_date);
END;
$$ LANGUAGE plpgsql;
```

### Database Functions for Common Queries
```sql
-- Get organization metrics summary
CREATE OR REPLACE FUNCTION get_organization_metrics(
    org_id UUID,
    period_days INTEGER DEFAULT 30
) RETURNS JSONB AS $$
DECLARE
    result JSONB;
BEGIN
    SELECT jsonb_build_object(
        'total_messages', COUNT(*) FILTER (WHERE direction = 'outbound'),
        'conversations', COUNT(DISTINCT conversation_id),
        'avg_response_time', AVG(generation_time_ms) FILTER (WHERE sender_type = 'ai'),
        'satisfaction_score', AVG(satisfaction_score)
    ) INTO result
    FROM platform.messages m
    JOIN platform.conversations c ON c.id = m.conversation_id
    WHERE c.organization_id = org_id
      AND m.created_at >= NOW() - (period_days || ' days')::INTERVAL;
    
    RETURN result;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## Backup & Maintenance

### Backup Strategy
```sql
-- Automated backup configuration
CREATE TABLE platform.backup_config (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    backup_type VARCHAR(20) NOT NULL, -- 'full', 'incremental'
    schedule_cron VARCHAR(100) NOT NULL,
    retention_days INTEGER NOT NULL,
    storage_location VARCHAR(500) NOT NULL,
    encryption_enabled BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Backup execution log
CREATE TABLE platform.backup_executions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    backup_config_id UUID REFERENCES platform.backup_config(id),
    status VARCHAR(20) DEFAULT 'running', -- 'running', 'completed', 'failed'
    started_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    completed_at TIMESTAMP WITH TIME ZONE,
    backup_size_bytes BIGINT,
    backup_location VARCHAR(500),
    error_message TEXT
);
```

### Data Retention Policies
```sql
-- Automatic cleanup of old data
CREATE OR REPLACE FUNCTION cleanup_old_data() RETURNS VOID AS $$
BEGIN
    -- Delete old audit logs (keep 1 year)
    DELETE FROM audit.activity_logs 
    WHERE created_at < NOW() - INTERVAL '1 year';
    
    -- Archive old agent executions (keep 6 months)
    DELETE FROM departments.agent_executions 
    WHERE created_at < NOW() - INTERVAL '6 months';
    
    -- Clean up old webhook events (keep 3 months)
    DELETE FROM integrations.webhook_events 
    WHERE created_at < NOW() - INTERVAL '3 months';
    
    -- Update statistics
    ANALYZE;
END;
$$ LANGUAGE plpgsql;
```

---

**Document Status:** Database schema design complete and ready for implementation. Includes multi-tenancy, security, performance optimizations, and comprehensive audit capabilities.