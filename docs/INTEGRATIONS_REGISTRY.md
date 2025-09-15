# External Integrations Registry
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Felipe PM + Claude  
**Status:** APPROVED  

---

## Integration Strategy Overview

### Core Principles
- **API-First**: All integrations through well-documented APIs
- **Security-First**: OAuth 2.0, token encryption, audit trails
- **Reliability**: Robust error handling, retries, failover
- **Compliance**: LGPD compliance for all data handling
- **User Experience**: Seamless setup and management

### Integration Categories
1. **Communication Channels**: WhatsApp, Instagram, Facebook
2. **Payment Processing**: Stripe, MercadoPago, PIX
3. **AI/ML Services**: OpenAI, Anthropic, Google AI
4. **Storage & CDN**: Google Cloud Storage, Cloudflare
5. **Analytics**: Google Analytics, Mixpanel, PostHog
6. **Monitoring**: Sentry, DataDog, OpenTelemetry

---

## Communication Channel Integrations

### 1. WhatsApp Cloud API

#### Integration Details
```yaml
integration_name: WhatsApp Cloud API
provider: Meta (Facebook)
category: communication
priority: critical
status: active
documentation_url: https://developers.facebook.com/docs/whatsapp/cloud-api

authentication:
  type: oauth2_bearer
  scopes: ["whatsapp_business_messaging"]
  token_expiry: never
  
rate_limits:
  tier_1: 1000_messages_per_day
  tier_2: 10000_messages_per_day
  tier_3: 100000_messages_per_day
  
webhook_events:
  - messages
  - message_status
  - account_alerts
  - business_capability_update
```

#### Implementation
```typescript
// WhatsApp integration service
export class WhatsAppIntegration {
  private baseUrl = 'https://graph.facebook.com/v18.0';
  
  async sendMessage(
    phoneNumberId: string,
    to: string,
    message: WhatsAppMessage
  ): Promise<WhatsAppResponse> {
    const response = await this.httpClient.post(
      `${this.baseUrl}/${phoneNumberId}/messages`,
      {
        messaging_product: 'whatsapp',
        to,
        type: message.type,
        [message.type]: message.content
      },
      {
        headers: {
          'Authorization': `Bearer ${this.accessToken}`,
          'Content-Type': 'application/json'
        }
      }
    );
    
    await this.trackUsage('message_sent', 1, phoneNumberId);
    return response.data;
  }
  
  async setupWebhook(
    organizationId: string,
    phoneNumberId: string
  ): Promise<void> {
    const webhookUrl = `${process.env.API_URL}/webhooks/whatsapp/${organizationId}`;
    const verifyToken = await this.generateVerifyToken(organizationId);
    
    await this.httpClient.post(
      `${this.baseUrl}/${phoneNumberId}/webhook`,
      {
        callback_url: webhookUrl,
        verify_token: verifyToken,
        events: ['messages', 'message_status']
      }
    );
  }
}
```

#### Webhook Processing
```typescript
// WhatsApp webhook handler
export class WhatsAppWebhookHandler {
  async processIncomingMessage(
    payload: WhatsAppWebhookPayload
  ): Promise<void> {
    const { entry } = payload;
    
    for (const change of entry[0].changes) {
      if (change.field === 'messages') {
        const message = change.value.messages?.[0];
        if (!message) continue;
        
        await this.queueMessageProcessing({
          organizationId: await this.getOrgFromPhoneNumber(
            change.value.metadata.phone_number_id
          ),
          messageId: message.id,
          from: message.from,
          text: message.text?.body || '',
          type: message.type,
          timestamp: new Date(parseInt(message.timestamp) * 1000)
        });
      }
    }
  }
  
  private async queueMessageProcessing(
    messageData: IncomingMessageData
  ): Promise<void> {
    await this.messageQueue.add('process-whatsapp-message', messageData, {
      priority: 1,
      attempts: 3,
      backoff: 'exponential'
    });
  }
}
```

### 2. Instagram Graph API

#### Integration Details
```yaml
integration_name: Instagram Graph API
provider: Meta (Facebook)
category: social_media
priority: high
status: active

authentication:
  type: oauth2
  scopes: ["instagram_basic", "instagram_content_publish", "pages_show_list"]
  token_expiry: 60_days
  refresh_required: true
  
rate_limits:
  api_calls: 200_per_hour
  content_publishing: 25_posts_per_day
  
supported_content_types:
  - image
  - video
  - carousel
  - story
```

#### Implementation
```typescript
export class InstagramIntegration {
  async publishContent(
    pageId: string,
    content: SocialMediaContent
  ): Promise<InstagramPublishResponse> {
    // Step 1: Create media container
    const container = await this.createMediaContainer(pageId, content);
    
    // Step 2: Publish the container
    const published = await this.publishMediaContainer(
      pageId, 
      container.id
    );
    
    await this.trackUsage('content_published', 1, pageId);
    return published;
  }
  
  private async createMediaContainer(
    pageId: string,
    content: SocialMediaContent
  ): Promise<MediaContainer> {
    const payload: any = {
      caption: content.caption,
      access_token: this.accessToken
    };
    
    if (content.mediaUrl) {
      payload.image_url = content.mediaUrl;
    }
    
    const response = await this.httpClient.post(
      `${this.baseUrl}/${pageId}/media`,
      payload
    );
    
    return response.data;
  }
  
  async getInsights(
    postId: string,
    metrics: string[]
  ): Promise<InstagramInsights> {
    const response = await this.httpClient.get(
      `${this.baseUrl}/${postId}/insights`,
      {
        params: {
          metric: metrics.join(','),
          access_token: this.accessToken
        }
      }
    );
    
    return response.data;
  }
}
```

### 3. Facebook Graph API

#### Integration Details
```yaml
integration_name: Facebook Graph API
provider: Meta (Facebook)
category: social_media
priority: high
status: active

authentication:
  type: oauth2
  scopes: ["pages_manage_posts", "pages_show_list", "pages_read_engagement"]
  token_expiry: 60_days
  
rate_limits:
  api_calls: 200_per_hour_per_user
  page_posts: 2_per_hour_per_page
  
supported_features:
  - page_posts
  - photo_posts
  - video_posts
  - post_insights
  - page_insights
```

---

## Payment Processing Integrations

### 1. Stripe Integration

#### Integration Details
```yaml
integration_name: Stripe
provider: Stripe Inc.
category: payment_processing
priority: critical
status: active
regions: [global]

authentication:
  type: api_key
  environments:
    test: sk_test_...
    live: sk_live_...
    
supported_features:
  - payment_intents
  - subscriptions
  - webhooks
  - customer_portal
  - connect_accounts
  
compliance:
  - pci_dss_level_1
  - sox_compliant
  - gdpr_compliant
```

#### Implementation
```typescript
export class StripeIntegration {
  private stripe: Stripe;
  
  constructor() {
    this.stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, {
      apiVersion: '2023-10-16'
    });
  }
  
  async createSubscription(
    customerId: string,
    priceId: string,
    metadata: Record<string, string>
  ): Promise<Stripe.Subscription> {
    const subscription = await this.stripe.subscriptions.create({
      customer: customerId,
      items: [{ price: priceId }],
      payment_behavior: 'default_incomplete',
      payment_settings: { save_default_payment_method: 'on_subscription' },
      expand: ['latest_invoice.payment_intent'],
      metadata
    });
    
    await this.trackUsage('subscription_created', 1);
    return subscription;
  }
  
  async handleWebhook(
    body: string,
    signature: string
  ): Promise<WebhookHandleResult> {
    const event = this.stripe.webhooks.constructEvent(
      body,
      signature,
      process.env.STRIPE_WEBHOOK_SECRET!
    );
    
    switch (event.type) {
      case 'customer.subscription.created':
        await this.handleSubscriptionCreated(event.data.object);
        break;
      case 'customer.subscription.updated':
        await this.handleSubscriptionUpdated(event.data.object);
        break;
      case 'invoice.payment_succeeded':
        await this.handlePaymentSucceeded(event.data.object);
        break;
      case 'invoice.payment_failed':
        await this.handlePaymentFailed(event.data.object);
        break;
    }
    
    return { processed: true };
  }
}
```

### 2. MercadoPago Integration (Brazil)

#### Integration Details
```yaml
integration_name: MercadoPago
provider: MercadoLibre
category: payment_processing
priority: critical
status: active
regions: [brazil, argentina, mexico]

authentication:
  type: oauth2
  scopes: ["read", "write"]
  
supported_payment_methods:
  - credit_card
  - debit_card
  - pix
  - boleto_bancario
  - account_money
  
local_features:
  brazil:
    - pix_instant_payment
    - boleto_bancario
    - installments_up_to_12x
```

#### Implementation
```typescript
export class MercadoPagoIntegration {
  async createPixPayment(
    amount: number,
    description: string,
    payerEmail: string
  ): Promise<PixPaymentResponse> {
    const payment = await this.httpClient.post(
      'https://api.mercadopago.com/v1/payments',
      {
        transaction_amount: amount,
        description,
        payment_method_id: 'pix',
        payer: { email: payerEmail }
      },
      {
        headers: {
          'Authorization': `Bearer ${this.accessToken}`,
          'Content-Type': 'application/json'
        }
      }
    );
    
    return {
      id: payment.data.id,
      status: payment.data.status,
      qr_code: payment.data.point_of_interaction.transaction_data.qr_code,
      qr_code_base64: payment.data.point_of_interaction.transaction_data.qr_code_base64,
      ticket_url: payment.data.point_of_interaction.transaction_data.ticket_url
    };
  }
}
```

---

## AI/ML Service Integrations

### 1. OpenAI Integration

#### Integration Details
```yaml
integration_name: OpenAI API
provider: OpenAI
category: ai_services
priority: critical
status: active

authentication:
  type: bearer_token
  
models:
  text_generation:
    - gpt-4-turbo
    - gpt-4
    - gpt-3.5-turbo
  image_generation:
    - dall-e-3
    - dall-e-2
  embeddings:
    - text-embedding-3-large
    - text-embedding-3-small
    
rate_limits:
  gpt_4: 10000_tokens_per_minute
  gpt_35_turbo: 90000_tokens_per_minute
  dall_e_3: 5_images_per_minute
```

#### Implementation
```typescript
export class OpenAIIntegration {
  private openai: OpenAI;
  
  async generateContent(
    prompt: string,
    context: ContentContext
  ): Promise<GeneratedContent> {
    const completion = await this.openai.chat.completions.create({
      model: context.model || 'gpt-4-turbo',
      messages: [
        {
          role: 'system',
          content: this.buildSystemPrompt(context)
        },
        {
          role: 'user',
          content: prompt
        }
      ],
      temperature: context.temperature || 0.7,
      max_tokens: context.maxTokens || 1000
    });
    
    await this.trackUsage(
      'tokens_consumed',
      completion.usage?.total_tokens || 0,
      context.organizationId
    );
    
    return {
      content: completion.choices[0].message.content!,
      model: completion.model,
      tokensUsed: completion.usage?.total_tokens || 0,
      finishReason: completion.choices[0].finish_reason
    };
  }
  
  async generateEmbedding(
    text: string,
    model: string = 'text-embedding-3-small'
  ): Promise<number[]> {
    const response = await this.openai.embeddings.create({
      model,
      input: text
    });
    
    return response.data[0].embedding;
  }
}
```

### 2. Anthropic Claude Integration

#### Integration Details
```yaml
integration_name: Anthropic Claude
provider: Anthropic
category: ai_services
priority: high
status: active

authentication:
  type: api_key
  
models:
  - claude-3-opus-20240229
  - claude-3-sonnet-20240229
  - claude-3-haiku-20240307
  
features:
  - long_context_window
  - function_calling
  - vision_capabilities
  
rate_limits:
  requests_per_minute: 1000
  tokens_per_minute: 80000
```

#### Implementation
```typescript
export class AnthropicIntegration {
  async generateContent(
    prompt: string,
    context: ContentContext
  ): Promise<GeneratedContent> {
    const response = await this.httpClient.post(
      'https://api.anthropic.com/v1/messages',
      {
        model: context.model || 'claude-3-sonnet-20240229',
        max_tokens: context.maxTokens || 1000,
        messages: [
          {
            role: 'user',
            content: prompt
          }
        ]
      },
      {
        headers: {
          'Authorization': `Bearer ${this.apiKey}`,
          'Content-Type': 'application/json',
          'anthropic-version': '2023-06-01'
        }
      }
    );
    
    return {
      content: response.data.content[0].text,
      model: response.data.model,
      tokensUsed: response.data.usage.output_tokens
    };
  }
}
```

---

## Storage & CDN Integrations

### 1. Google Cloud Storage

#### Integration Details
```yaml
integration_name: Google Cloud Storage
provider: Google Cloud
category: storage
priority: critical
status: active

authentication:
  type: service_account
  credentials_path: /secrets/gcs-service-account.json
  
buckets:
  public_assets: ai-departments-public
  private_assets: ai-departments-private
  backups: ai-departments-backups
  
features:
  - object_lifecycle_management
  - versioning
  - encryption_at_rest
  - signed_urls
```

#### Implementation
```typescript
export class GoogleCloudStorageIntegration {
  private storage: Storage;
  
  async uploadFile(
    bucketName: string,
    fileName: string,
    fileBuffer: Buffer,
    options: UploadOptions = {}
  ): Promise<UploadResult> {
    const bucket = this.storage.bucket(bucketName);
    const file = bucket.file(fileName);
    
    const stream = file.createWriteStream({
      metadata: {
        contentType: options.contentType,
        metadata: options.metadata
      },
      public: options.makePublic || false
    });
    
    return new Promise((resolve, reject) => {
      stream.on('error', reject);
      stream.on('finish', () => {
        resolve({
          fileName,
          publicUrl: options.makePublic 
            ? `https://storage.googleapis.com/${bucketName}/${fileName}`
            : undefined,
          gsUrl: `gs://${bucketName}/${fileName}`
        });
      });
      
      stream.end(fileBuffer);
    });
  }
  
  async generateSignedUrl(
    bucketName: string,
    fileName: string,
    expiresIn: number = 3600
  ): Promise<string> {
    const options = {
      version: 'v4' as const,
      action: 'read' as const,
      expires: Date.now() + expiresIn * 1000
    };
    
    const [url] = await this.storage
      .bucket(bucketName)
      .file(fileName)
      .getSignedUrl(options);
    
    return url;
  }
}
```

### 2. Cloudflare CDN

#### Integration Details
```yaml
integration_name: Cloudflare
provider: Cloudflare Inc.
category: cdn
priority: high
status: active

features:
  - global_cdn
  - ddos_protection
  - ssl_certificates
  - image_optimization
  - cache_purging
  
zones:
  - aidepartments.com.br
  - assets.aidepartments.com.br
  
cache_rules:
  static_assets: 1_year
  api_responses: 5_minutes
  images: 1_month
```

---

## Analytics & Monitoring Integrations

### 1. Google Analytics 4

#### Integration Details
```yaml
integration_name: Google Analytics 4
provider: Google
category: analytics
priority: medium
status: active

measurement_id: G-XXXXXXXXXX
tracking_scope: frontend_only

events_tracked:
  - page_view
  - user_signup
  - subscription_purchase
  - content_generated
  - agent_interaction
  
custom_dimensions:
  - organization_plan
  - business_type
  - user_role
```

#### Implementation
```typescript
export class GoogleAnalyticsIntegration {
  async trackEvent(
    eventName: string,
    parameters: Record<string, any>,
    userId?: string
  ): Promise<void> {
    await this.httpClient.post(
      `https://www.google-analytics.com/mp/collect?measurement_id=${this.measurementId}&api_secret=${this.apiSecret}`,
      {
        client_id: this.generateClientId(userId),
        user_id: userId,
        events: [
          {
            name: eventName,
            params: parameters
          }
        ]
      }
    );
  }
  
  async trackConversion(
    conversionEvent: string,
    value: number,
    currency: string = 'BRL'
  ): Promise<void> {
    await this.trackEvent(conversionEvent, {
      value,
      currency,
      transaction_id: this.generateTransactionId()
    });
  }
}
```

### 2. Sentry Error Monitoring

#### Integration Details
```yaml
integration_name: Sentry
provider: Sentry Inc.
category: monitoring
priority: critical
status: active

dsn: https://xxx@sentry.io/xxx
environment: production
release: 1.0.0

features:
  - error_tracking
  - performance_monitoring
  - release_tracking
  - user_feedback
  
integrations:
  - express
  - postgres
  - redis
  - openai
```

#### Implementation
```typescript
export class SentryIntegration {
  static initialize(): void {
    Sentry.init({
      dsn: process.env.SENTRY_DSN,
      environment: process.env.NODE_ENV,
      release: process.env.APP_VERSION,
      tracesSampleRate: 0.1,
      profilesSampleRate: 0.1,
      
      integrations: [
        new Sentry.Integrations.Http({ tracing: true }),
        new Sentry.Integrations.Express({ app }),
        new Sentry.Integrations.Postgres(),
        new Sentry.Integrations.Redis()
      ]
    });
  }
  
  static captureException(
    error: Error,
    context?: Record<string, any>
  ): void {
    Sentry.withScope((scope) => {
      if (context) {
        Object.keys(context).forEach(key => {
          scope.setTag(key, context[key]);
        });
      }
      Sentry.captureException(error);
    });
  }
}
```

---

## Integration Management Framework

### Integration Lifecycle Management

```typescript
export class IntegrationManager {
  async registerIntegration(
    organizationId: string,
    integrationType: string,
    credentials: IntegrationCredentials
  ): Promise<Integration> {
    // Validate credentials
    const validation = await this.validateCredentials(
      integrationType,
      credentials
    );
    
    if (!validation.valid) {
      throw new InvalidCredentialsError(validation.error);
    }
    
    // Encrypt sensitive data
    const encryptedCredentials = await this.encryptCredentials(
      credentials,
      organizationId
    );
    
    // Store integration
    const integration = await this.db.integrations.create({
      organizationId,
      type: integrationType,
      credentials: encryptedCredentials,
      status: 'active',
      createdAt: new Date()
    });
    
    // Setup webhooks if required
    if (this.requiresWebhook(integrationType)) {
      await this.setupWebhook(integration);
    }
    
    // Track integration event
    await this.analytics.track('integration_connected', {
      organizationId,
      integrationType
    });
    
    return integration;
  }
  
  async testIntegration(
    integrationId: string
  ): Promise<IntegrationTestResult> {
    const integration = await this.getIntegration(integrationId);
    const service = this.getIntegrationService(integration.type);
    
    try {
      const result = await service.testConnection(integration.credentials);
      
      await this.db.integrations.update(integrationId, {
        lastTestedAt: new Date(),
        testResult: 'success'
      });
      
      return { success: true, details: result };
    } catch (error) {
      await this.db.integrations.update(integrationId, {
        lastTestedAt: new Date(),
        testResult: 'failed',
        lastError: error.message
      });
      
      return { success: false, error: error.message };
    }
  }
}
```

### Error Handling & Retry Logic

```typescript
export class IntegrationErrorHandler {
  async handleIntegrationError(
    integration: Integration,
    error: IntegrationError,
    context: ErrorContext
  ): Promise<ErrorHandleResult> {
    // Log error
    await this.logger.error('Integration error', {
      integrationId: integration.id,
      error: error.message,
      context
    });
    
    // Determine retry strategy
    const retryStrategy = this.getRetryStrategy(error.type);
    
    if (retryStrategy.shouldRetry) {
      await this.scheduleRetry(
        integration,
        context,
        retryStrategy.delayMs
      );
      
      return { action: 'retry_scheduled' };
    }
    
    // Check if integration should be disabled
    if (this.shouldDisableIntegration(error)) {
      await this.disableIntegration(integration.id);
      await this.notifyOrganization(
        integration.organizationId,
        'integration_disabled',
        { integration: integration.type, reason: error.message }
      );
      
      return { action: 'integration_disabled' };
    }
    
    return { action: 'error_logged' };
  }
  
  private getRetryStrategy(errorType: string): RetryStrategy {
    const strategies: Record<string, RetryStrategy> = {
      'rate_limit': {
        shouldRetry: true,
        delayMs: 60000,
        maxRetries: 3
      },
      'temporary_failure': {
        shouldRetry: true,
        delayMs: 5000,
        maxRetries: 5
      },
      'authentication_error': {
        shouldRetry: false,
        delayMs: 0,
        maxRetries: 0
      }
    };
    
    return strategies[errorType] || strategies['temporary_failure'];
  }
}
```

### Integration Monitoring & Health Checks

```typescript
export class IntegrationMonitor {
  async performHealthChecks(): Promise<void> {
    const activeIntegrations = await this.db.integrations.findActive();
    
    const healthCheckPromises = activeIntegrations.map(integration =>
      this.checkIntegrationHealth(integration)
    );
    
    const results = await Promise.allSettled(healthCheckPromises);
    
    // Process results and alert on failures
    for (let i = 0; i < results.length; i++) {
      const result = results[i];
      const integration = activeIntegrations[i];
      
      if (result.status === 'rejected') {
        await this.handleHealthCheckFailure(integration, result.reason);
      } else {
        await this.updateHealthStatus(integration.id, 'healthy');
      }
    }
  }
  
  private async checkIntegrationHealth(
    integration: Integration
  ): Promise<HealthCheckResult> {
    const service = this.getIntegrationService(integration.type);
    
    const startTime = Date.now();
    const result = await service.healthCheck(integration.credentials);
    const responseTime = Date.now() - startTime;
    
    return {
      integrationId: integration.id,
      healthy: result.healthy,
      responseTime,
      details: result.details
    };
  }
}
```

---

## Security & Compliance

### Credential Management

```typescript
export class CredentialManager {
  async encryptCredentials(
    credentials: IntegrationCredentials,
    organizationId: string
  ): Promise<string> {
    const encryptionKey = await this.getOrganizationEncryptionKey(
      organizationId
    );
    
    const encrypted = await encrypt(
      JSON.stringify(credentials),
      encryptionKey
    );
    
    return encrypted;
  }
  
  async decryptCredentials(
    encryptedCredentials: string,
    organizationId: string
  ): Promise<IntegrationCredentials> {
    const encryptionKey = await this.getOrganizationEncryptionKey(
      organizationId
    );
    
    const decrypted = await decrypt(encryptedCredentials, encryptionKey);
    return JSON.parse(decrypted);
  }
  
  async rotateCredentials(
    integrationId: string
  ): Promise<void> {
    const integration = await this.getIntegration(integrationId);
    const service = this.getIntegrationService(integration.type);
    
    if (service.supportsCredentialRotation) {
      const newCredentials = await service.rotateCredentials(
        integration.credentials
      );
      
      const encrypted = await this.encryptCredentials(
        newCredentials,
        integration.organizationId
      );
      
      await this.db.integrations.update(integrationId, {
        credentials: encrypted,
        lastRotatedAt: new Date()
      });
    }
  }
}
```

### Audit Trail

```typescript
export class IntegrationAuditService {
  async logIntegrationEvent(
    event: IntegrationAuditEvent
  ): Promise<void> {
    await this.db.auditLogs.create({
      organizationId: event.organizationId,
      integrationId: event.integrationId,
      action: event.action,
      userId: event.userId,
      details: event.details,
      ipAddress: event.ipAddress,
      userAgent: event.userAgent,
      timestamp: new Date()
    });
    
    // Alert on sensitive actions
    if (this.isSensitiveAction(event.action)) {
      await this.alertingService.sendAlert({
        type: 'integration_sensitive_action',
        severity: 'high',
        details: event
      });
    }
  }
  
  private isSensitiveAction(action: string): boolean {
    const sensitiveActions = [
      'credentials_rotated',
      'integration_disabled',
      'webhook_modified',
      'permissions_changed'
    ];
    
    return sensitiveActions.includes(action);
  }
}
```

---

**Document Status:** External integrations registry complete with detailed implementation guides, security protocols, and monitoring frameworks. Ready for development and deployment.