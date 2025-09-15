# Coding Standards & Best Practices
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Felipe PM + Claude  
**Status:** DEVELOPMENT READY  

---

## Overview

Comprehensive coding standards for the AI Departments Platform, ensuring maintainable, scalable, and consistent code across the entire stack. These standards support our meta-strategy of transparent development while maintaining professional code quality.

**Philosophy:** Write code that our AI can understand, extend, and explain to customers as part of our transparent development approach.

---

## 1. General Development Principles

### 1.1 Core Principles
```yaml
development_philosophy:
  transparency_first:
    principle: "Code should be readable and explainable to non-technical stakeholders"
    implementation: "Clear naming, comprehensive comments, self-documenting APIs"
    meta_strategy_alignment: "Code quality demonstrates platform capabilities"
    
  ai_first_development:
    principle: "Write code that AI can easily understand and extend"
    implementation: "Consistent patterns, clear interfaces, comprehensive type definitions"
    tooling: "AI-assisted code review, automated documentation generation"
    
  brazilian_market_focus:
    principle: "Code should reflect Brazilian business context and requirements"
    implementation: "Localization-first, LGPD compliance, regional considerations"
    examples: "Date formats, currency handling, cultural contexts"
    
  performance_conscious:
    principle: "Optimize for cost-efficiency and user experience"
    implementation: "Efficient algorithms, lazy loading, appropriate caching"
    monitoring: "Performance metrics integrated into development workflow"
```

### 1.2 Code Quality Standards
```typescript
// Code quality standards example
interface CodeQualityStandards {
  readability: {
    cyclomaticComplexity: 'max 10 per function';
    functionLength: 'max 50 lines';
    classLength: 'max 300 lines';
    nesting: 'max 4 levels deep';
  };
  
  maintainability: {
    duplication: 'DRY principle - no code duplication > 3 lines';
    coupling: 'Low coupling between modules';
    cohesion: 'High cohesion within modules';
    singleResponsibility: 'One responsibility per function/class';
  };
  
  reliability: {
    errorHandling: 'All functions must handle errors gracefully';
    nullChecks: 'All inputs validated for null/undefined';
    boundaryCases: 'Edge cases explicitly handled';
    gracefulDegradation: 'Fallbacks for external service failures';
  };
}
```

---

## 2. TypeScript/JavaScript Standards

### 2.1 Language Configuration
```json
// tsconfig.json - TypeScript Configuration
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "forceConsistentCasingInFileNames": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "preserve",
    "incremental": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/utils/*": ["./src/utils/*"],
      "@/types/*": ["./src/types/*"],
      "@/hooks/*": ["./src/hooks/*"],
      "@/services/*": ["./src/services/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

### 2.2 Naming Conventions
```typescript
// naming_conventions.ts - Comprehensive naming standards

// Variables and Functions - camelCase
const userAuthToken = 'abc123';
const calculateBrazilianTax = (amount: number) => amount * 0.18;

// Constants - SCREAMING_SNAKE_CASE
const MAX_RETRIES = 3;
const DEFAULT_TIMEOUT_MS = 5000;
const BRAZILIAN_TAX_RATE = 0.18;

// Classes - PascalCase
class WhatsAppMessageProcessor {
  private messageQueue: Array<Message>;
  
  public processMessage(message: Message): ProcessedMessage {
    // Implementation
  }
}

// Interfaces and Types - PascalCase with descriptive names
interface BrazilianBusinessProfile {
  readonly cnpj: string;
  readonly businessName: string;
  readonly businessType: BusinessType;
  readonly address: BrazilianAddress;
}

// Enums - PascalCase for enum, SCREAMING_SNAKE_CASE for values
enum BusinessType {
  MEI = 'MEI',
  MICRO_ENTERPRISE = 'MICRO_ENTERPRISE',
  SMALL_BUSINESS = 'SMALL_BUSINESS',
  MEDIUM_BUSINESS = 'MEDIUM_BUSINESS'
}

// File names - kebab-case
// user-auth-service.ts
// brazilian-tax-calculator.ts
// whatsapp-message-processor.ts

// Component files - PascalCase
// UserProfile.tsx
// DashboardMetrics.tsx
// WhatsAppChat.tsx

// API routes - kebab-case with clear resource names
// /api/users/profile
// /api/departments/marketing
// /api/integrations/whatsapp
```

### 2.3 Function and Method Standards
```typescript
// function_standards.ts - Function writing best practices

// Good: Pure functions with clear inputs/outputs
function calculateBrazilianBusinessTax(
  grossRevenue: number,
  businessType: BusinessType,
  state: BrazilianState
): TaxCalculationResult {
  // Input validation
  if (grossRevenue < 0) {
    throw new Error('Revenue cannot be negative');
  }
  
  if (!Object.values(BusinessType).includes(businessType)) {
    throw new Error(`Invalid business type: ${businessType}`);
  }
  
  // Business logic
  const federalTax = calculateFederalTax(grossRevenue, businessType);
  const stateTax = calculateStateTax(grossRevenue, state);
  const municipalTax = calculateMunicipalTax(grossRevenue);
  
  return {
    grossRevenue,
    federalTax,
    stateTax,
    municipalTax,
    totalTax: federalTax + stateTax + municipalTax,
    netRevenue: grossRevenue - (federalTax + stateTax + municipalTax),
    effectiveRate: ((federalTax + stateTax + municipalTax) / grossRevenue) * 100
  };
}

// Good: Async functions with proper error handling
async function sendWhatsAppMessage(
  message: WhatsAppMessage
): Promise<MessageDeliveryResult> {
  try {
    // Pre-flight validation
    validateWhatsAppMessage(message);
    
    // Rate limiting check
    await checkRateLimit(message.recipientPhone);
    
    // Send message
    const deliveryResponse = await whatsAppApi.sendMessage({
      to: message.recipientPhone,
      body: message.content,
      type: message.type
    });
    
    // Log successful delivery
    await logMessageDelivery({
      messageId: deliveryResponse.messageId,
      recipientPhone: message.recipientPhone,
      deliveredAt: new Date(),
      status: 'delivered'
    });
    
    return {
      success: true,
      messageId: deliveryResponse.messageId,
      deliveredAt: new Date()
    };
    
  } catch (error) {
    // Structured error handling
    if (error instanceof RateLimitError) {
      return {
        success: false,
        error: 'RATE_LIMITED',
        retryAfter: error.retryAfter
      };
    }
    
    if (error instanceof ValidationError) {
      return {
        success: false,
        error: 'INVALID_MESSAGE',
        details: error.details
      };
    }
    
    // Log unexpected errors
    await logError('whatsapp_send_failure', error, {
      recipientPhone: message.recipientPhone,
      messageType: message.type
    });
    
    return {
      success: false,
      error: 'DELIVERY_FAILED',
      message: 'Unable to deliver message'
    };
  }
}

// Good: Higher-order functions for reusability
function withRetry<T>(
  operation: () => Promise<T>,
  maxRetries: number = MAX_RETRIES,
  backoffMs: number = 1000
) {
  return async (): Promise<T> => {
    let lastError: Error;
    
    for (let attempt = 1; attempt <= maxRetries; attempt++) {
      try {
        return await operation();
      } catch (error) {
        lastError = error as Error;
        
        if (attempt === maxRetries) {
          throw lastError;
        }
        
        // Exponential backoff
        await new Promise(resolve => 
          setTimeout(resolve, backoffMs * Math.pow(2, attempt - 1))
        );
      }
    }
    
    throw lastError!;
  };
}
```

### 2.4 Error Handling Standards
```typescript
// error_handling_standards.ts

// Custom Error Classes
class BusinessLogicError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly context?: Record<string, unknown>
  ) {
    super(message);
    this.name = 'BusinessLogicError';
  }
}

class ValidationError extends Error {
  constructor(
    message: string,
    public readonly field: string,
    public readonly value: unknown
  ) {
    super(message);
    this.name = 'ValidationError';
  }
}

class ExternalServiceError extends Error {
  constructor(
    message: string,
    public readonly service: string,
    public readonly statusCode?: number,
    public readonly originalError?: Error
  ) {
    super(message);
    this.name = 'ExternalServiceError';
  }
}

// Result Pattern for Better Error Handling
type Result<T, E = Error> = 
  | { success: true; data: T }
  | { success: false; error: E };

// Usage example
async function createSocialMediaPost(
  postData: SocialMediaPostInput
): Promise<Result<SocialMediaPost, ValidationError | ExternalServiceError>> {
  // Validate input
  const validationResult = validatePostData(postData);
  if (!validationResult.isValid) {
    return {
      success: false,
      error: new ValidationError(
        'Invalid post data',
        validationResult.field,
        validationResult.value
      )
    };
  }
  
  try {
    // Generate content with AI
    const aiContent = await generatePostContent(postData);
    
    // Create post
    const post = await createPost({
      ...postData,
      aiGeneratedContent: aiContent,
      createdAt: new Date()
    });
    
    return {
      success: true,
      data: post
    };
    
  } catch (error) {
    if (error instanceof AIServiceError) {
      return {
        success: false,
        error: new ExternalServiceError(
          'AI content generation failed',
          'openai',
          error.statusCode,
          error
        )
      };
    }
    
    throw error; // Re-throw unexpected errors
  }
}
```

---

## 3. React/Next.js Standards

### 3.1 Component Architecture
```typescript
// component_architecture.tsx

// Component File Structure Standard
// components/
//   ├── ui/           # Reusable UI components
//   ├── forms/        # Form components
//   ├── layout/       # Layout components
//   ├── features/     # Feature-specific components
//   └── pages/        # Page-level components

// Good: Functional Component with TypeScript
interface DashboardMetricsProps {
  readonly organizationId: string;
  readonly dateRange: DateRange;
  readonly className?: string;
  readonly onMetricsLoad?: (metrics: Metrics) => void;
}

export const DashboardMetrics: React.FC<DashboardMetricsProps> = ({
  organizationId,
  dateRange,
  className,
  onMetricsLoad
}) => {
  // State management
  const [metrics, setMetrics] = useState<Metrics | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);
  
  // Custom hooks
  const { formatCurrency } = useBrazilianFormatting();
  const { trackEvent } = useAnalytics();
  
  // Effects
  useEffect(() => {
    let cancelled = false;
    
    const loadMetrics = async () => {
      try {
        setLoading(true);
        setError(null);
        
        const result = await fetchOrganizationMetrics({
          organizationId,
          dateRange
        });
        
        if (cancelled) return;
        
        if (result.success) {
          setMetrics(result.data);
          onMetricsLoad?.(result.data);
          
          trackEvent('dashboard_metrics_loaded', {
            organizationId,
            metricsCount: Object.keys(result.data).length
          });
        } else {
          setError(result.error);
        }
      } catch (err) {
        if (cancelled) return;
        setError(err as Error);
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    };
    
    loadMetrics();
    
    return () => {
      cancelled = true;
    };
  }, [organizationId, dateRange, onMetricsLoad, trackEvent]);
  
  // Render helpers
  const renderMetricCard = (key: string, metric: Metric) => (
    <MetricCard
      key={key}
      title={metric.label}
      value={formatCurrency(metric.value)}
      trend={metric.trend}
      icon={metric.icon}
    />
  );
  
  // Loading state
  if (loading) {
    return (
      <div className={cn('grid grid-cols-2 md:grid-cols-4 gap-4', className)}>
        {Array.from({ length: 4 }).map((_, i) => (
          <MetricCardSkeleton key={i} />
        ))}
      </div>
    );
  }
  
  // Error state
  if (error) {
    return (
      <ErrorBoundary
        error={error}
        onRetry={() => window.location.reload()}
        className={className}
      />
    );
  }
  
  // Empty state
  if (!metrics || Object.keys(metrics).length === 0) {
    return (
      <EmptyState
        title="Nenhuma métrica disponível"
        description="Métricas aparecerão aqui após suas primeiras atividades."
        action={
          <Button variant="primary" size="sm">
            Ver Tutorial
          </Button>
        }
        className={className}
      />
    );
  }
  
  // Main render
  return (
    <div className={cn('grid grid-cols-2 md:grid-cols-4 gap-4', className)}>
      {Object.entries(metrics).map(([key, metric]) =>
        renderMetricCard(key, metric)
      )}
    </div>
  );
};

// Component display name for debugging
DashboardMetrics.displayName = 'DashboardMetrics';
```

### 3.2 Custom Hooks Standards
```typescript
// custom_hooks_standards.ts

// Good: Custom hook with clear responsibilities
interface UseBrazilianFormattingReturn {
  formatCurrency: (value: number, currency?: string) => string;
  formatPhone: (phone: string) => string;
  formatCPF: (cpf: string) => string;
  formatCNPJ: (cnpj: string) => string;
  formatDate: (date: Date, format?: 'short' | 'long') => string;
}

export const useBrazilianFormatting = (): UseBrazilianFormattingReturn => {
  const formatCurrency = useCallback((
    value: number, 
    currency: string = 'BRL'
  ): string => {
    return new Intl.NumberFormat('pt-BR', {
      style: 'currency',
      currency
    }).format(value);
  }, []);
  
  const formatPhone = useCallback((phone: string): string => {
    // Remove all non-digits
    const digits = phone.replace(/\D/g, '');
    
    // Brazilian mobile phone format: +55 (11) 99999-9999
    if (digits.length === 13 && digits.startsWith('55')) {
      const country = digits.slice(0, 2);
      const area = digits.slice(2, 4);
      const number1 = digits.slice(4, 9);
      const number2 = digits.slice(9, 13);
      
      return `+${country} (${area}) ${number1}-${number2}`;
    }
    
    return phone; // Return original if can't format
  }, []);
  
  const formatCPF = useCallback((cpf: string): string => {
    const digits = cpf.replace(/\D/g, '');
    
    if (digits.length === 11) {
      return `${digits.slice(0, 3)}.${digits.slice(3, 6)}.${digits.slice(6, 9)}-${digits.slice(9, 11)}`;
    }
    
    return cpf;
  }, []);
  
  const formatCNPJ = useCallback((cnpj: string): string => {
    const digits = cnpj.replace(/\D/g, '');
    
    if (digits.length === 14) {
      return `${digits.slice(0, 2)}.${digits.slice(2, 5)}.${digits.slice(5, 8)}/${digits.slice(8, 12)}-${digits.slice(12, 14)}`;
    }
    
    return cnpj;
  }, []);
  
  const formatDate = useCallback((
    date: Date, 
    format: 'short' | 'long' = 'short'
  ): string => {
    const options: Intl.DateTimeFormatOptions = format === 'long'
      ? { 
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric'
        }
      : {
          year: 'numeric',
          month: '2-digit',
          day: '2-digit'
        };
    
    return new Intl.DateTimeFormat('pt-BR', options).format(date);
  }, []);
  
  return {
    formatCurrency,
    formatPhone,
    formatCPF,
    formatCNPJ,
    formatDate
  };
};

// Good: Hook for API data fetching with caching
interface UseApiDataOptions<T> {
  enabled?: boolean;
  refreshInterval?: number;
  onSuccess?: (data: T) => void;
  onError?: (error: Error) => void;
}

interface UseApiDataReturn<T> {
  data: T | null;
  loading: boolean;
  error: Error | null;
  refetch: () => Promise<void>;
  mutate: (newData: T) => void;
}

export function useApiData<T>(
  endpoint: string,
  options: UseApiDataOptions<T> = {}
): UseApiDataReturn<T> {
  const {
    enabled = true,
    refreshInterval,
    onSuccess,
    onError
  } = options;
  
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(enabled);
  const [error, setError] = useState<Error | null>(null);
  
  const fetchData = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);
      
      const response = await apiClient.get<T>(endpoint);
      
      if (response.success) {
        setData(response.data);
        onSuccess?.(response.data);
      } else {
        const error = new Error(response.error.message);
        setError(error);
        onError?.(error);
      }
    } catch (err) {
      const error = err as Error;
      setError(error);
      onError?.(error);
    } finally {
      setLoading(false);
    }
  }, [endpoint, onSuccess, onError]);
  
  const mutate = useCallback((newData: T) => {
    setData(newData);
  }, []);
  
  useEffect(() => {
    if (enabled) {
      fetchData();
    }
  }, [enabled, fetchData]);
  
  useEffect(() => {
    if (refreshInterval && enabled) {
      const interval = setInterval(fetchData, refreshInterval);
      return () => clearInterval(interval);
    }
  }, [refreshInterval, enabled, fetchData]);
  
  return {
    data,
    loading,
    error,
    refetch: fetchData,
    mutate
  };
}
```

### 3.3 Form Handling Standards
```typescript
// form_handling_standards.tsx

// Good: Form with validation and Brazilian-specific logic
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

// Brazilian business profile validation schema
const businessProfileSchema = z.object({
  businessName: z
    .string()
    .min(2, 'Nome deve ter pelo menos 2 caracteres')
    .max(100, 'Nome deve ter no máximo 100 caracteres'),
    
  cnpj: z
    .string()
    .regex(/^\d{2}\.\d{3}\.\d{3}\/\d{4}-\d{2}$/, 'CNPJ deve estar no formato XX.XXX.XXX/XXXX-XX')
    .refine(validateCNPJ, 'CNPJ inválido'),
    
  businessType: z.nativeEnum(BusinessType),
  
  phone: z
    .string()
    .regex(/^\+55\s\(\d{2}\)\s\d{4,5}-\d{4}$/, 'Telefone deve estar no formato +55 (XX) XXXXX-XXXX'),
    
  email: z
    .string()
    .email('Email inválido')
    .toLowerCase(),
    
  address: z.object({
    cep: z
      .string()
      .regex(/^\d{5}-\d{3}$/, 'CEP deve estar no formato XXXXX-XXX'),
    street: z.string().min(1, 'Endereço é obrigatório'),
    number: z.string().min(1, 'Número é obrigatório'),
    neighborhood: z.string().min(1, 'Bairro é obrigatório'),
    city: z.string().min(1, 'Cidade é obrigatória'),
    state: z.nativeEnum(BrazilianState),
    complement: z.string().optional()
  })
});

type BusinessProfileForm = z.infer<typeof businessProfileSchema>;

interface BusinessProfileFormProps {
  initialData?: Partial<BusinessProfileForm>;
  onSubmit: (data: BusinessProfileForm) => Promise<void>;
  onCancel?: () => void;
}

export const BusinessProfileForm: React.FC<BusinessProfileFormProps> = ({
  initialData,
  onSubmit,
  onCancel
}) => {
  const [submitting, setSubmitting] = useState(false);
  
  const form = useForm<BusinessProfileForm>({
    resolver: zodResolver(businessProfileSchema),
    defaultValues: {
      businessName: '',
      cnpj: '',
      businessType: BusinessType.MEI,
      phone: '+55 ',
      email: '',
      address: {
        cep: '',
        street: '',
        number: '',
        neighborhood: '',
        city: '',
        state: BrazilianState.SAO_PAULO,
        complement: ''
      },
      ...initialData
    }
  });
  
  const { handleSubmit, formState: { errors, isDirty } } = form;
  
  // Auto-fill address from CEP
  const watchedCEP = form.watch('address.cep');
  useEffect(() => {
    if (watchedCEP && /^\d{5}-\d{3}$/.test(watchedCEP)) {
      fetchAddressByCEP(watchedCEP).then(address => {
        if (address) {
          form.setValue('address.street', address.street);
          form.setValue('address.neighborhood', address.neighborhood);
          form.setValue('address.city', address.city);
          form.setValue('address.state', address.state as BrazilianState);
        }
      });
    }
  }, [watchedCEP, form]);
  
  const onFormSubmit = async (data: BusinessProfileForm) => {
    try {
      setSubmitting(true);
      await onSubmit(data);
    } catch (error) {
      toast.error('Erro ao salvar perfil. Tente novamente.');
    } finally {
      setSubmitting(false);
    }
  };
  
  return (
    <Form {...form}>
      <form onSubmit={handleSubmit(onFormSubmit)} className="space-y-6">
        <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
          <FormField
            control={form.control}
            name="businessName"
            render={({ field }) => (
              <FormItem>
                <FormLabel>Nome da Empresa</FormLabel>
                <FormControl>
                  <Input
                    {...field}
                    placeholder="Ex: Boutique da Maria"
                    autoComplete="organization"
                  />
                </FormControl>
                <FormMessage />
              </FormItem>
            )}
          />
          
          <FormField
            control={form.control}
            name="cnpj"
            render={({ field }) => (
              <FormItem>
                <FormLabel>CNPJ</FormLabel>
                <FormControl>
                  <Input
                    {...field}
                    placeholder="XX.XXX.XXX/XXXX-XX"
                    onChange={(e) => {
                      const formatted = formatCNPJInput(e.target.value);
                      field.onChange(formatted);
                    }}
                  />
                </FormControl>
                <FormDescription>
                  Deixe em branco se for MEI
                </FormDescription>
                <FormMessage />
              </FormItem>
            )}
          />
        </div>
        
        {/* Address Section */}
        <div className="space-y-4">
          <h3 className="text-lg font-medium">Endereço</h3>
          
          <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
            <FormField
              control={form.control}
              name="address.cep"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>CEP</FormLabel>
                  <FormControl>
                    <Input
                      {...field}
                      placeholder="XXXXX-XXX"
                      onChange={(e) => {
                        const formatted = formatCEPInput(e.target.value);
                        field.onChange(formatted);
                      }}
                    />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            
            <FormField
              control={form.control}
              name="address.state"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Estado</FormLabel>
                  <Select onValueChange={field.onChange} defaultValue={field.value}>
                    <FormControl>
                      <SelectTrigger>
                        <SelectValue placeholder="Selecione o estado" />
                      </SelectTrigger>
                    </FormControl>
                    <SelectContent>
                      {Object.values(BrazilianState).map(state => (
                        <SelectItem key={state} value={state}>
                          {brazilianStateNames[state]}
                        </SelectItem>
                      ))}
                    </SelectContent>
                  </Select>
                  <FormMessage />
                </FormItem>
              )}
            />
          </div>
        </div>
        
        {/* Form Actions */}
        <div className="flex justify-between">
          {onCancel && (
            <Button
              type="button"
              variant="outline"
              onClick={onCancel}
              disabled={submitting}
            >
              Cancelar
            </Button>
          )}
          
          <Button
            type="submit"
            disabled={submitting || !isDirty}
            className="ml-auto"
          >
            {submitting && <Loader2 className="mr-2 h-4 w-4 animate-spin" />}
            {submitting ? 'Salvando...' : 'Salvar Perfil'}
          </Button>
        </div>
      </form>
    </Form>
  );
};
```

---

## 4. Python/FastAPI Standards

### 4.1 Python Configuration
```python
# pyproject.toml - Python project configuration
[tool.poetry]
name = "ai-departments-backend"
version = "1.0.0"
description = "AI Departments Platform Backend"
authors = ["Felipe PM <felipe@aidepartments.com>"]

[tool.poetry.dependencies]
python = "^3.11"
fastapi = "^0.104.1"
uvicorn = "^0.24.0"
sqlalchemy = "^2.0.23"
alembic = "^1.13.0"
pydantic = "^2.5.0"
pydantic-settings = "^2.1.0"
asyncpg = "^0.29.0"
redis = "^5.0.1"
openai = "^1.3.7"
anthropic = "^0.8.1"
google-generativeai = "^0.3.2"
celery = "^5.3.4"
prometheus-client = "^0.19.0"
structlog = "^23.2.0"

[tool.poetry.group.dev.dependencies]
pytest = "^7.4.3"
pytest-asyncio = "^0.21.1"
black = "^23.11.0"
isort = "^5.12.0"
flake8 = "^6.1.0"
mypy = "^1.7.1"
pre-commit = "^3.6.0"

[tool.black]
line-length = 88
target-version = ['py311']
include = '\.pyi?$'
extend-exclude = '''
/(
  # directories
  \.eggs
  | \.git
  | \.hg
  | \.mypy_cache
  | \.tox
  | \.venv
  | build
  | dist
)/
'''

[tool.isort]
profile = "black"
multi_line_output = 3
line_length = 88
known_first_party = ["app"]

[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
disallow_incomplete_defs = true
check_untyped_defs = true
disallow_untyped_decorators = true
no_implicit_optional = true
warn_redundant_casts = true
warn_unused_ignores = true
warn_no_return = true
warn_unreachable = true
strict_equality = true
```

### 4.2 FastAPI Standards
```python
# fastapi_standards.py - FastAPI application structure

from datetime import datetime
from typing import Any, Dict, List, Optional
from uuid import UUID

import structlog
from fastapi import Depends, FastAPI, HTTPException, Request, status
from fastapi.middleware.cors import CORSMiddleware
from fastapi.responses import JSONResponse
from pydantic import BaseModel, Field, validator
from sqlalchemy.ext.asyncio import AsyncSession

# Configure structured logging
logger = structlog.get_logger(__name__)

# Application factory pattern
def create_app() -> FastAPI:
    """Create and configure FastAPI application."""
    app = FastAPI(
        title="AI Departments Platform API",
        description="Backend API for AI-powered business departments",
        version="1.0.0",
        docs_url="/api/docs",
        redoc_url="/api/redoc",
        openapi_url="/api/openapi.json"
    )
    
    # Configure CORS for Brazilian and international access
    app.add_middleware(
        CORSMiddleware,
        allow_origins=[
            "https://app.aidepartments.com.br",
            "https://aidepartments.com.br",
            "http://localhost:3000",  # Development
        ],
        allow_credentials=True,
        allow_methods=["GET", "POST", "PUT", "DELETE", "PATCH"],
        allow_headers=["*"],
    )
    
    # Include routers
    from app.routers import (
        auth_router,
        organizations_router,
        departments_router,
        integrations_router,
    )
    
    app.include_router(auth_router, prefix="/api/auth", tags=["Authentication"])
    app.include_router(organizations_router, prefix="/api/organizations", tags=["Organizations"])
    app.include_router(departments_router, prefix="/api/departments", tags=["Departments"])
    app.include_router(integrations_router, prefix="/api/integrations", tags=["Integrations"])
    
    # Global exception handler
    @app.exception_handler(Exception)
    async def global_exception_handler(request: Request, exc: Exception):
        logger.error(
            "Unhandled exception",
            exc_info=exc,
            request_url=str(request.url),
            request_method=request.method
        )
        
        return JSONResponse(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            content={
                "error": "internal_server_error",
                "message": "An unexpected error occurred",
                "request_id": getattr(request.state, 'request_id', None)
            }
        )
    
    return app

# Pydantic models with Brazilian context
class BrazilianPhoneNumber(str):
    """Brazilian phone number validation."""
    
    @classmethod
    def __get_validators__(cls):
        yield cls.validate
    
    @classmethod
    def validate(cls, v: str) -> str:
        if not isinstance(v, str):
            raise TypeError('string required')
        
        # Remove all non-digits
        digits = ''.join(filter(str.isdigit, v))
        
        # Brazilian mobile: 5511999887766 (country + area + number)
        if len(digits) == 13 and digits.startswith('55'):
            return v
        
        # Brazilian landline: 551133334444
        if len(digits) == 12 and digits.startsWith('55'):
            return v
            
        raise ValueError('Invalid Brazilian phone number format')

class BrazilianBusinessProfile(BaseModel):
    """Brazilian business profile with local validation."""
    
    business_name: str = Field(..., min_length=2, max_length=100)
    cnpj: Optional[str] = Field(None, regex=r'^\d{14}$')
    phone: BrazilianPhoneNumber
    email: str = Field(..., regex=r'^[^@]+@[^@]+\.[^@]+$')
    
    # Address with Brazilian specifics
    cep: str = Field(..., regex=r'^\d{8}$')
    state: str = Field(..., regex=r'^[A-Z]{2}$')  # Brazilian state codes
    city: str = Field(..., min_length=1, max_length=100)
    
    # Business context
    business_type: str = Field(..., regex=r'^(MEI|MICRO|SMALL|MEDIUM)$')
    industry: str = Field(..., min_length=1, max_length=50)
    
    @validator('cnpj')
    def validate_cnpj(cls, v: Optional[str]) -> Optional[str]:
        """Validate Brazilian CNPJ number."""
        if v is None:
            return v
            
        # Basic CNPJ validation algorithm
        if not v.isdigit() or len(v) != 14:
            raise ValueError('CNPJ must contain exactly 14 digits')
        
        # CNPJ validation algorithm implementation
        # (simplified for example - full algorithm would be implemented)
        return v
    
    class Config:
        schema_extra = {
            "example": {
                "business_name": "Boutique da Maria",
                "cnpj": "12345678000195",
                "phone": "+5511999887766", 
                "email": "maria@boutique.com.br",
                "cep": "01234567",
                "state": "SP",
                "city": "São Paulo",
                "business_type": "MEI",
                "industry": "fashion_retail"
            }
        }

# Service layer with dependency injection
class OrganizationService:
    """Business logic for organization management."""
    
    def __init__(self, db: AsyncSession):
        self.db = db
    
    async def create_organization(
        self, 
        profile: BrazilianBusinessProfile,
        user_id: UUID
    ) -> Dict[str, Any]:
        """Create new organization with Brazilian business context."""
        
        try:
            # Check if CNPJ already exists (if provided)
            if profile.cnpj:
                existing = await self._get_organization_by_cnpj(profile.cnpj)
                if existing:
                    raise HTTPException(
                        status_code=status.HTTP_409_CONFLICT,
                        detail="CNPJ already registered"
                    )
            
            # Create organization
            org_data = {
                **profile.dict(),
                "owner_id": user_id,
                "created_at": datetime.utcnow(),
                "timezone": "America/Sao_Paulo",  # Default Brazilian timezone
                "currency": "BRL",
                "locale": "pt-BR"
            }
            
            organization = await self._create_organization(org_data)
            
            # Initialize default departments for Brazilian market
            await self._initialize_default_departments(organization.id)
            
            # Set up default integrations
            await self._setup_default_integrations(organization.id)
            
            logger.info(
                "Organization created successfully",
                organization_id=organization.id,
                business_name=profile.business_name,
                user_id=user_id
            )
            
            return {
                "id": organization.id,
                "business_name": organization.business_name,
                "status": "active",
                "created_at": organization.created_at
            }
            
        except Exception as e:
            logger.error(
                "Failed to create organization",
                error=str(e),
                user_id=user_id,
                business_name=profile.business_name
            )
            raise
    
    async def _initialize_default_departments(self, org_id: UUID) -> None:
        """Initialize departments optimized for Brazilian market."""
        
        default_departments = [
            {
                "type": "marketing",
                "config": {
                    "platforms": ["instagram", "facebook", "whatsapp"],
                    "posting_schedule": "business_hours_brazil",
                    "language": "pt-BR",
                    "cultural_context": "brazilian"
                }
            },
            {
                "type": "customer_service", 
                "config": {
                    "channels": ["whatsapp", "email"],
                    "business_hours": "09:00-18:00 GMT-3",
                    "auto_responses": True,
                    "language": "pt-BR"
                }
            }
        ]
        
        for dept_config in default_departments:
            await self._create_department(org_id, dept_config)

# API Router with proper error handling
from fastapi import APIRouter, Depends
from app.dependencies import get_current_user, get_db_session
from app.services import OrganizationService

router = APIRouter()

@router.post("/", response_model=Dict[str, Any])
async def create_organization(
    profile: BrazilianBusinessProfile,
    current_user: Dict = Depends(get_current_user),
    db: AsyncSession = Depends(get_db_session)
):
    """Create new Brazilian business organization."""
    
    service = OrganizationService(db)
    
    try:
        result = await service.create_organization(
            profile=profile,
            user_id=current_user["id"]
        )
        
        return {
            "success": True,
            "data": result,
            "message": "Organização criada com sucesso"
        }
        
    except HTTPException:
        raise  # Re-raise HTTP exceptions as-is
        
    except Exception as e:
        logger.error(
            "Unexpected error creating organization",
            error=str(e),
            user_id=current_user["id"]
        )
        
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="Failed to create organization"
        )
```

---

## 5. Database Standards

### 5.1 SQL Standards
```sql
-- database_standards.sql

-- Table naming: snake_case, descriptive names
-- Always plural for table names
CREATE TABLE organizations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    business_name VARCHAR(255) NOT NULL,
    cnpj VARCHAR(14) UNIQUE, -- Brazilian business identifier
    email VARCHAR(320) NOT NULL,
    phone VARCHAR(20) NOT NULL,
    
    -- Brazilian address fields
    cep VARCHAR(8) NOT NULL,
    state VARCHAR(2) NOT NULL, -- Brazilian state codes
    city VARCHAR(100) NOT NULL,
    street_address TEXT NOT NULL,
    address_complement TEXT,
    
    -- Business context
    business_type business_type_enum NOT NULL,
    industry VARCHAR(50) NOT NULL,
    
    -- Localization
    timezone VARCHAR(50) NOT NULL DEFAULT 'America/Sao_Paulo',
    currency VARCHAR(3) NOT NULL DEFAULT 'BRL',
    locale VARCHAR(5) NOT NULL DEFAULT 'pt-BR',
    
    -- Metadata
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at TIMESTAMPTZ,
    
    -- Indexes for performance
    CONSTRAINT organizations_email_key UNIQUE (email),
    CONSTRAINT organizations_cnpj_key UNIQUE (cnpj) WHERE cnpj IS NOT NULL
);

-- Enum types for type safety
CREATE TYPE business_type_enum AS ENUM (
    'MEI',              -- Microempreendedor Individual
    'MICRO_ENTERPRISE', -- Microempresa
    'SMALL_BUSINESS',   -- Empresa de Pequeno Porte
    'MEDIUM_BUSINESS'   -- Empresa de Médio Porte
);

CREATE TYPE department_type_enum AS ENUM (
    'marketing',
    'customer_service',
    'sales_crm', 
    'finance',
    'design',
    'data_bi',
    'growth_ads',
    'automation'
);

-- Departments configuration
CREATE TABLE department_configs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    department_type department_type_enum NOT NULL,
    
    -- JSON configuration specific to department
    config JSONB NOT NULL DEFAULT '{}',
    
    -- Status and controls
    is_active BOOLEAN NOT NULL DEFAULT true,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    
    -- Ensure one config per department type per organization
    CONSTRAINT department_configs_org_type_key 
        UNIQUE (organization_id, department_type)
);

-- AI-generated content with audit trail
CREATE TABLE ai_generated_content (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    department_type department_type_enum NOT NULL,
    
    -- Content details
    content_type VARCHAR(50) NOT NULL, -- 'social_post', 'email', 'whatsapp_message'
    content_data JSONB NOT NULL,
    
    -- AI generation metadata
    ai_model VARCHAR(50) NOT NULL,     -- 'gpt-4', 'gemini-pro', etc.
    generation_cost_cents INTEGER,     -- Cost in centavos (Brazilian cents)
    generation_time_ms INTEGER,        -- Response time in milliseconds
    prompt_tokens INTEGER,
    completion_tokens INTEGER,
    
    -- Content lifecycle
    status VARCHAR(20) NOT NULL DEFAULT 'generated', -- 'generated', 'approved', 'published', 'rejected'
    published_at TIMESTAMPTZ,
    
    -- Audit trail
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    created_by UUID REFERENCES users(id),
    
    -- Performance indexes
    INDEX idx_ai_content_org_dept (organization_id, department_type),
    INDEX idx_ai_content_created_at (created_at DESC),
    INDEX idx_ai_content_status (status) WHERE status = 'published'
);

-- WhatsApp conversations for customer service
CREATE TABLE whatsapp_conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    
    -- Customer information
    customer_phone VARCHAR(20) NOT NULL,
    customer_name VARCHAR(255),
    customer_id UUID REFERENCES customers(id),
    
    -- Conversation metadata
    status VARCHAR(20) NOT NULL DEFAULT 'active', -- 'active', 'resolved', 'archived'
    priority VARCHAR(10) NOT NULL DEFAULT 'normal', -- 'low', 'normal', 'high', 'urgent'
    
    -- AI vs Human handling
    is_ai_handled BOOLEAN NOT NULL DEFAULT true,
    human_agent_id UUID REFERENCES users(id),
    escalated_at TIMESTAMPTZ,
    
    -- Satisfaction and resolution
    resolution_category VARCHAR(50),
    satisfaction_rating INTEGER CHECK (satisfaction_rating >= 1 AND satisfaction_rating <= 5),
    
    -- Timestamps
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    last_message_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    resolved_at TIMESTAMPTZ,
    
    -- Indexes for performance
    INDEX idx_whatsapp_conv_org_status (organization_id, status),
    INDEX idx_whatsapp_conv_phone (customer_phone),
    INDEX idx_whatsapp_conv_created_at (created_at DESC)
);

-- WhatsApp messages within conversations
CREATE TABLE whatsapp_messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    conversation_id UUID NOT NULL REFERENCES whatsapp_conversations(id) ON DELETE CASCADE,
    
    -- Message content
    message_type VARCHAR(20) NOT NULL DEFAULT 'text', -- 'text', 'image', 'audio', 'document'
    content TEXT NOT NULL,
    media_url TEXT,
    
    -- Message direction and handling
    direction VARCHAR(10) NOT NULL, -- 'inbound', 'outbound'  
    sender_type VARCHAR(10) NOT NULL, -- 'customer', 'ai_agent', 'human_agent'
    
    -- AI processing metadata (for AI responses)
    ai_model VARCHAR(50),
    ai_confidence DECIMAL(3,2) CHECK (ai_confidence >= 0 AND ai_confidence <= 1),
    processing_time_ms INTEGER,
    
    -- WhatsApp API metadata
    whatsapp_message_id VARCHAR(255) UNIQUE,
    delivery_status VARCHAR(20), -- 'sent', 'delivered', 'read', 'failed'
    
    -- Timestamps
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    delivered_at TIMESTAMPTZ,
    read_at TIMESTAMPTZ,
    
    -- Indexes
    INDEX idx_whatsapp_msg_conversation (conversation_id, created_at DESC),
    INDEX idx_whatsapp_msg_whatsapp_id (whatsapp_message_id) WHERE whatsapp_message_id IS NOT NULL
);

-- Business transactions for financial tracking
CREATE TABLE business_transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    
    -- Transaction details
    transaction_date DATE NOT NULL,
    description TEXT NOT NULL,
    amount_cents INTEGER NOT NULL, -- Amount in centavos (Brazilian cents)
    currency VARCHAR(3) NOT NULL DEFAULT 'BRL',
    
    -- Categorization
    transaction_type VARCHAR(20) NOT NULL, -- 'income', 'expense'
    category VARCHAR(50) NOT NULL,
    subcategory VARCHAR(50),
    
    -- Payment method (Brazilian context)
    payment_method VARCHAR(30), -- 'pix', 'credit_card', 'debit_card', 'cash', 'bank_transfer'
    
    -- Tax information (Brazilian tax context)
    is_tax_deductible BOOLEAN NOT NULL DEFAULT false,
    tax_category VARCHAR(30),
    
    -- Attachments and documentation
    receipt_url TEXT,
    invoice_number VARCHAR(100),
    
    -- AI categorization metadata
    ai_categorized BOOLEAN NOT NULL DEFAULT false,
    ai_category_confidence DECIMAL(3,2),
    manual_review_required BOOLEAN NOT NULL DEFAULT false,
    
    -- Audit trail
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    created_by UUID REFERENCES users(id),
    
    -- Indexes for financial reporting
    INDEX idx_transactions_org_date (organization_id, transaction_date DESC),
    INDEX idx_transactions_type_category (transaction_type, category),
    INDEX idx_transactions_payment_method (payment_method)
);

-- Comprehensive view for Brazilian business dashboard
CREATE VIEW organization_dashboard_metrics AS
SELECT 
    o.id as organization_id,
    o.business_name,
    o.business_type,
    
    -- Revenue metrics (current month)
    COALESCE(SUM(
        CASE 
            WHEN bt.transaction_type = 'income' 
            AND DATE_TRUNC('month', bt.transaction_date) = DATE_TRUNC('month', CURRENT_DATE)
            THEN bt.amount_cents 
            ELSE 0 
        END
    ), 0) as current_month_revenue_cents,
    
    -- Expense metrics (current month)  
    COALESCE(SUM(
        CASE 
            WHEN bt.transaction_type = 'expense'
            AND DATE_TRUNC('month', bt.transaction_date) = DATE_TRUNC('month', CURRENT_DATE)
            THEN bt.amount_cents
            ELSE 0
        END
    ), 0) as current_month_expenses_cents,
    
    -- Customer service metrics
    COUNT(DISTINCT wc.id) FILTER (
        WHERE wc.created_at >= DATE_TRUNC('month', CURRENT_DATE)
    ) as monthly_conversations,
    
    AVG(wc.satisfaction_rating) FILTER (
        WHERE wc.satisfaction_rating IS NOT NULL
        AND wc.created_at >= DATE_TRUNC('month', CURRENT_DATE)
    ) as avg_satisfaction_rating,
    
    -- AI-generated content metrics
    COUNT(agc.id) FILTER (
        WHERE agc.created_at >= DATE_TRUNC('month', CURRENT_DATE)
    ) as monthly_ai_content_generated,
    
    SUM(agc.generation_cost_cents) FILTER (
        WHERE agc.created_at >= DATE_TRUNC('month', CURRENT_DATE)
    ) as monthly_ai_cost_cents

FROM organizations o
LEFT JOIN business_transactions bt ON bt.organization_id = o.id
LEFT JOIN whatsapp_conversations wc ON wc.organization_id = o.id  
LEFT JOIN ai_generated_content agc ON agc.organization_id = o.id
WHERE o.deleted_at IS NULL
GROUP BY o.id, o.business_name, o.business_type;
```

---

## 6. API Standards

### 6.1 REST API Design
```yaml
# api_design_standards.yaml
rest_api_principles:
  url_structure:
    base_url: "https://api.aidepartments.com.br/v1"
    versioning: "URL path versioning (/v1, /v2)"
    resource_naming: "Plural nouns (users, organizations, departments)"
    hierarchy: "Logical resource hierarchy (/organizations/{id}/departments)"
    
  http_methods:
    GET: "Retrieve resources (idempotent, cacheable)"
    POST: "Create new resources"
    PUT: "Replace entire resource (idempotent)"
    PATCH: "Partial resource update"
    DELETE: "Remove resource (idempotent)"
    
  status_codes:
    200: "OK - Successful GET, PUT, PATCH"
    201: "Created - Successful POST"
    204: "No Content - Successful DELETE"
    400: "Bad Request - Client error, validation failed"
    401: "Unauthorized - Authentication required"
    403: "Forbidden - Authentication insufficient"
    404: "Not Found - Resource doesn't exist"
    409: "Conflict - Resource already exists"
    422: "Unprocessable Entity - Validation error"
    500: "Internal Server Error - Server error"
    
  response_format:
    success_response:
      structure: |
        {
          "success": true,
          "data": {...},
          "message": "Operation completed successfully",
          "request_id": "uuid",
          "timestamp": "2024-12-13T10:00:00Z"
        }
    
    error_response:
      structure: |
        {
          "success": false,
          "error": {
            "code": "VALIDATION_ERROR",
            "message": "Invalid input data",
            "details": {
              "field": "email",
              "reason": "Invalid email format"
            }
          },
          "request_id": "uuid",
          "timestamp": "2024-12-13T10:00:00Z"
        }
    
    pagination:
      structure: |
        {
          "success": true,
          "data": [...],
          "pagination": {
            "page": 1,
            "limit": 20,
            "total": 150,
            "pages": 8,
            "has_next": true,
            "has_prev": false
          }
        }

# Example API endpoints
api_endpoints:
  authentication:
    - "POST /auth/login"
    - "POST /auth/logout" 
    - "POST /auth/refresh"
    - "POST /auth/register"
    - "POST /auth/reset-password"
    
  organizations:
    - "GET /organizations"
    - "POST /organizations"
    - "GET /organizations/{id}"
    - "PUT /organizations/{id}"
    - "DELETE /organizations/{id}"
    
  departments:
    - "GET /organizations/{org_id}/departments"
    - "POST /organizations/{org_id}/departments"
    - "GET /departments/{dept_id}"
    - "PATCH /departments/{dept_id}/config"
    - "POST /departments/{dept_id}/activate"
    - "POST /departments/{dept_id}/deactivate"
    
  ai_content:
    - "POST /departments/{dept_id}/generate-content"
    - "GET /departments/{dept_id}/content"
    - "PUT /content/{content_id}/approve" 
    - "POST /content/{content_id}/publish"
    - "DELETE /content/{content_id}"
```

### 6.2 GraphQL Standards (Future)
```graphql
# graphql_schema.graphql - Future GraphQL API design

type Organization {
  id: ID!
  businessName: String!
  cnpj: String
  email: String!
  phone: String!
  
  # Brazilian address
  address: BrazilianAddress!
  
  # Business context
  businessType: BusinessType!
  industry: String!
  
  # Departments
  departments: [Department!]!
  
  # Metrics
  metrics(period: MetricsPeriod = CURRENT_MONTH): OrganizationMetrics
  
  # Metadata
  createdAt: DateTime!
  updatedAt: DateTime!
}

type BrazilianAddress {
  cep: String!
  state: BrazilianState!
  city: String!
  streetAddress: String!
  complement: String
}

enum BusinessType {
  MEI
  MICRO_ENTERPRISE
  SMALL_BUSINESS
  MEDIUM_BUSINESS
}

enum BrazilianState {
  SP # São Paulo
  RJ # Rio de Janeiro  
  MG # Minas Gerais
  # ... other states
}

type Department {
  id: ID!
  type: DepartmentType!
  isActive: Boolean!
  config: JSON!
  
  # Performance metrics
  metrics(period: MetricsPeriod = CURRENT_MONTH): DepartmentMetrics
  
  # Generated content
  content(
    first: Int = 20
    after: String
    status: ContentStatus
  ): ContentConnection!
}

enum DepartmentType {
  MARKETING
  CUSTOMER_SERVICE
  SALES_CRM
  FINANCE
  DESIGN
  DATA_BI
  GROWTH_ADS
  AUTOMATION
}

# Mutations
type Mutation {
  # Organization management
  createOrganization(input: CreateOrganizationInput!): Organization!
  updateOrganization(id: ID!, input: UpdateOrganizationInput!): Organization!
  
  # Department management
  activateDepartment(organizationId: ID!, type: DepartmentType!): Department!
  updateDepartmentConfig(departmentId: ID!, config: JSON!): Department!
  
  # Content generation
  generateContent(departmentId: ID!, input: ContentGenerationInput!): AIGeneratedContent!
  approveContent(contentId: ID!): AIGeneratedContent!
  publishContent(contentId: ID!): AIGeneratedContent!
}

# Queries
type Query {
  # Current user context
  me: User!
  myOrganizations: [Organization!]!
  
  # Organization data
  organization(id: ID!): Organization
  
  # AI content
  aiGeneratedContent(
    organizationId: ID!
    departmentType: DepartmentType
    status: ContentStatus
    first: Int = 20
    after: String
  ): ContentConnection!
  
  # Analytics
  organizationMetrics(
    organizationId: ID!
    period: MetricsPeriod!
  ): OrganizationMetrics!
}
```

---

## 7. Testing Standards

### 7.1 Testing Framework Configuration
```typescript
// jest.config.js - Testing configuration
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/src/test/setup.ts'],
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
  testMatch: [
    '**/__tests__/**/*.(ts|tsx)',
    '**/*.(test|spec).(ts|tsx)'
  ],
  collectCoverageFrom: [
    'src/**/*.(ts|tsx)',
    '!src/**/*.d.ts',
    '!src/test/**/*',
    '!src/**/*.stories.*',
  ],
  coverageThresholds: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },
  testTimeout: 10000,
};
```

### 7.2 Test Structure Standards
```typescript
// test_standards.test.ts - Testing best practices

import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { jest } from '@jest/globals';
import { BrazilianBusinessProfileForm } from '@/components/forms/BusinessProfileForm';
import { createMockOrganization, createMockUser } from '@/test/factories';

// Test file organization:
// 1. Imports
// 2. Test data and mocks
// 3. Setup/teardown
// 4. Test suites grouped by functionality
// 5. Helper functions at the end

describe('BrazilianBusinessProfileForm', () => {
  // Mock setup
  const mockOnSubmit = jest.fn();
  const mockOnCancel = jest.fn();
  
  // Test data factories
  const validBusinessProfile = {
    businessName: 'Boutique da Maria',
    cnpj: '12345678000195',
    phone: '+55 (11) 99988-7766',
    email: 'maria@boutique.com.br',
    address: {
      cep: '01234-567',
      street: 'Rua das Flores',
      number: '123',
      neighborhood: 'Vila Madalena',
      city: 'São Paulo',
      state: 'SP',
      complement: 'Apt 45'
    },
    businessType: 'MEI',
    industry: 'fashion_retail'
  };
  
  beforeEach(() => {
    // Reset mocks before each test
    jest.clearAllMocks();
  });
  
  describe('Form Rendering', () => {
    it('should render all required fields', () => {
      render(
        <BrazilianBusinessProfileForm
          onSubmit={mockOnSubmit}
          onCancel={mockOnCancel}
        />
      );
      
      // Test presence of all required fields
      expect(screen.getByLabelText(/nome da empresa/i)).toBeInTheDocument();
      expect(screen.getByLabelText(/cnpj/i)).toBeInTheDocument();
      expect(screen.getByLabelText(/telefone/i)).toBeInTheDocument();
      expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
      expect(screen.getByLabelText(/cep/i)).toBeInTheDocument();
    });
    
    it('should populate form with initial data when provided', () => {
      render(
        <BrazilianBusinessProfileForm
          initialData={validBusinessProfile}
          onSubmit={mockOnSubmit}
          onCancel={mockOnCancel}
        />
      );
      
      expect(screen.getByDisplayValue('Boutique da Maria')).toBeInTheDocument();
      expect(screen.getByDisplayValue('12.345.678/0001-95')).toBeInTheDocument(); // Formatted CNPJ
    });
  });
  
  describe('Form Validation', () => {
    it('should validate required fields', async () => {
      render(
        <BrazilianBusinessProfileForm
          onSubmit={mockOnSubmit}
          onCancel={mockOnCancel}
        />
      );
      
      const submitButton = screen.getByText(/salvar perfil/i);
      fireEvent.click(submitButton);
      
      await waitFor(() => {
        expect(screen.getByText(/nome é obrigatório/i)).toBeInTheDocument();
      });
    });
    
    it('should validate Brazilian CNPJ format', async () => {
      render(
        <BrazilianBusinessProfileForm
          onSubmit={mockOnSubmit}
          onCancel={mockOnCancel}
        />
      );
      
      const cnpjInput = screen.getByLabelText(/cnpj/i);
      fireEvent.change(cnpjInput, { target: { value: '123' } });
      
      const submitButton = screen.getByText(/salvar perfil/i);
      fireEvent.click(submitButton);
      
      await waitFor(() => {
        expect(screen.getByText(/cnpj deve estar no formato/i)).toBeInTheDocument();
      });
    });
    
    it('should validate Brazilian phone number format', async () => {
      render(
        <BrazilianBusinessProfileForm
          onSubmit={mockOnSubmit}
          onCancel={mockOnCancel}
        />
      );
      
      const phoneInput = screen.getByLabelText(/telefone/i);
      fireEvent.change(phoneInput, { target: { value: '123' } });
      
      const submitButton = screen.getByText(/salvar perfil/i);
      fireEvent.click(submitButton);
      
      await waitFor(() => {
        expect(screen.getByText(/telefone deve estar no formato/i)).toBeInTheDocument();
      });
    });
  });
  
  describe('Brazilian Address Auto-fill', () => {
    it('should auto-fill address when valid CEP is entered', async () => {
      // Mock the CEP API call
      const mockFetchAddressByCEP = jest.fn().mockResolvedValue({
        street: 'Avenida Paulista',
        neighborhood: 'Bela Vista',
        city: 'São Paulo',
        state: 'SP'
      });
      
      jest.mock('@/services/cep-service', () => ({
        fetchAddressByCEP: mockFetchAddressByCEP
      }));
      
      render(
        <BrazilianBusinessProfileForm
          onSubmit={mockOnSubmit}
          onCancel={mockOnCancel}
        />
      );
      
      const cepInput = screen.getByLabelText(/cep/i);
      fireEvent.change(cepInput, { target: { value: '01310-100' } });
      
      await waitFor(() => {
        expect(mockFetchAddressByCEP).toHaveBeenCalledWith('01310100');
      });
      
      await waitFor(() => {
        expect(screen.getByDisplayValue('Avenida Paulista')).toBeInTheDocument();
      });
    });
  });
  
  describe('Form Submission', () => {
    it('should submit form with valid data', async () => {
      render(
        <BrazilianBusinessProfileForm
          onSubmit={mockOnSubmit}
          onCancel={mockOnCancel}
        />
      );
      
      // Fill out the form
      await fillOutValidForm(validBusinessProfile);
      
      const submitButton = screen.getByText(/salvar perfil/i);
      fireEvent.click(submitButton);
      
      await waitFor(() => {
        expect(mockOnSubmit).toHaveBeenCalledWith({
          ...validBusinessProfile,
          cnpj: '12345678000195', // Unformatted for API
        });
      });
    });
    
    it('should handle submission errors gracefully', async () => {
      const mockSubmitWithError = jest.fn().mockRejectedValue(
        new Error('Network error')
      );
      
      render(
        <BrazilianBusinessProfileForm
          onSubmit={mockSubmitWithError}
          onCancel={mockOnCancel}
        />
      );
      
      await fillOutValidForm(validBusinessProfile);
      
      const submitButton = screen.getByText(/salvar perfil/i);
      fireEvent.click(submitButton);
      
      await waitFor(() => {
        expect(screen.getByText(/erro ao salvar perfil/i)).toBeInTheDocument();
      });
    });
  });
  
  describe('Accessibility', () => {
    it('should have proper ARIA labels', () => {
      render(
        <BrazilianBusinessProfileForm
          onSubmit={mockOnSubmit}
          onCancel={mockOnCancel}
        />
      );
      
      // Check for proper labeling
      expect(screen.getByLabelText(/nome da empresa/i)).toHaveAttribute('aria-required', 'true');
      expect(screen.getByLabelText(/email/i)).toHaveAttribute('type', 'email');
    });
    
    it('should announce form errors to screen readers', async () => {
      render(
        <BrazilianBusinessProfileForm
          onSubmit={mockOnSubmit}
          onCancel={mockOnCancel}
        />
      );
      
      const submitButton = screen.getByText(/salvar perfil/i);
      fireEvent.click(submitButton);
      
      await waitFor(() => {
        const errorMessage = screen.getByText(/nome é obrigatório/i);
        expect(errorMessage).toHaveAttribute('role', 'alert');
      });
    });
  });
});

// Helper functions for test setup
async function fillOutValidForm(data: typeof validBusinessProfile) {
  const businessNameInput = screen.getByLabelText(/nome da empresa/i);
  fireEvent.change(businessNameInput, { target: { value: data.businessName } });
  
  const cnpjInput = screen.getByLabelText(/cnpj/i);
  fireEvent.change(cnpjInput, { target: { value: data.cnpj } });
  
  const phoneInput = screen.getByLabelText(/telefone/i);
  fireEvent.change(phoneInput, { target: { value: data.phone } });
  
  const emailInput = screen.getByLabelText(/email/i);
  fireEvent.change(emailInput, { target: { value: data.email } });
  
  // ... fill other fields
}

// Integration test example
describe('Business Profile Integration', () => {
  it('should complete the full business setup flow', async () => {
    // This would test the entire flow from form submission
    // through API call to success state
    const mockApiResponse = createMockOrganization();
    
    // Mock API client
    jest.mock('@/services/api-client', () => ({
      post: jest.fn().mockResolvedValue({
        success: true,
        data: mockApiResponse
      })
    }));
    
    render(<BusinessSetupFlow />);
    
    // Fill and submit form
    await fillOutValidForm(validBusinessProfile);
    
    const submitButton = screen.getByText(/salvar perfil/i);
    fireEvent.click(submitButton);
    
    // Verify success state
    await waitFor(() => {
      expect(screen.getByText(/perfil criado com sucesso/i)).toBeInTheDocument();
    });
  });
});
```

---

## 8. Performance Standards

### 8.1 Frontend Performance
```typescript
// performance_standards.ts

// Performance budgets and monitoring
const PERFORMANCE_BUDGETS = {
  // Core Web Vitals targets
  largest_contentful_paint: 2500, // 2.5s
  first_input_delay: 100,         // 100ms
  cumulative_layout_shift: 0.1,   // 0.1
  
  // Loading performance
  time_to_interactive: 3800,      // 3.8s
  first_contentful_paint: 1800,   // 1.8s
  
  // Bundle sizes
  main_bundle: 250000,            // 250KB gzipped
  vendor_bundle: 300000,          // 300KB gzipped
  
  // Resource counts
  total_requests: 50,
  image_requests: 20,
  font_requests: 4
};

// Performance monitoring setup
export const setupPerformanceMonitoring = () => {
  // Core Web Vitals monitoring
  import('web-vitals').then(({ getCLS, getFID, getFCP, getLCP, getTTFB }) => {
    getCLS(console.log);
    getFID(console.log);
    getFCP(console.log);
    getLCP(console.log);
    getTTFB(console.log);
  });
  
  // Custom performance marks
  performance.mark('app-start');
  
  // Monitor route changes
  performance.mark('route-change-start');
};

// Lazy loading implementation
export const LazyComponent = React.lazy(() => 
  import('./ExpensiveComponent').then(module => ({
    default: module.ExpensiveComponent
  }))
);

// Image optimization component
interface OptimizedImageProps {
  src: string;
  alt: string;
  width?: number;
  height?: number;
  priority?: boolean;
}

export const OptimizedImage: React.FC<OptimizedImageProps> = ({
  src,
  alt,
  width,
  height,
  priority = false
}) => {
  const [isLoaded, setIsLoaded] = useState(false);
  const [error, setError] = useState(false);
  
  return (
    <div className="relative overflow-hidden">
      {/* Placeholder while loading */}
      {!isLoaded && !error && (
        <div 
          className="animate-pulse bg-gray-200"
          style={{ width, height }}
        />
      )}
      
      {/* Actual image */}
      <Image
        src={src}
        alt={alt}
        width={width}
        height={height}
        priority={priority}
        loading={priority ? 'eager' : 'lazy'}
        onLoad={() => setIsLoaded(true)}
        onError={() => setError(true)}
        className={`transition-opacity duration-300 ${
          isLoaded ? 'opacity-100' : 'opacity-0'
        }`}
      />
      
      {/* Error fallback */}
      {error && (
        <div 
          className="flex items-center justify-center bg-gray-100 text-gray-500"
          style={{ width, height }}
        >
          Failed to load image
        </div>
      )}
    </div>
  );
};

// Code splitting by route
export const AppRoutes = () => {
  return (
    <Routes>
      <Route path="/" element={
        <Suspense fallback={<PageSkeleton />}>
          <LazyComponent />
        </Suspense>
      } />
    </Routes>
  );
};
```

### 8.2 Backend Performance
```python
# performance_standards.py

import asyncio
import time
from functools import wraps
from typing import Any, Callable, Dict, Optional
import structlog

logger = structlog.get_logger(__name__)

# Performance monitoring decorators
def monitor_performance(operation_name: str):
    """Decorator to monitor function performance."""
    
    def decorator(func: Callable) -> Callable:
        @wraps(func)
        async def wrapper(*args, **kwargs) -> Any:
            start_time = time.perf_counter()
            
            try:
                result = await func(*args, **kwargs)
                
                end_time = time.perf_counter()
                duration_ms = (end_time - start_time) * 1000
                
                logger.info(
                    "Operation completed",
                    operation=operation_name,
                    duration_ms=round(duration_ms, 2),
                    success=True
                )
                
                return result
                
            except Exception as e:
                end_time = time.perf_counter()
                duration_ms = (end_time - start_time) * 1000
                
                logger.error(
                    "Operation failed",
                    operation=operation_name,
                    duration_ms=round(duration_ms, 2),
                    error=str(e),
                    success=False
                )
                
                raise
                
        return wrapper
    return decorator

# Database query optimization
class DatabaseOptimizations:
    """Database performance optimization utilities."""
    
    @staticmethod
    async def batch_fetch_organizations(
        org_ids: list[str],
        db: AsyncSession
    ) -> Dict[str, Any]:
        """Fetch multiple organizations in a single query."""
        
        if not org_ids:
            return {}
            
        query = select(Organization).where(
            Organization.id.in_(org_ids)
        ).options(
            # Eager load related data to avoid N+1 queries
            selectinload(Organization.departments),
            selectinload(Organization.integrations)
        )
        
        result = await db.execute(query)
        organizations = result.scalars().all()
        
        return {org.id: org for org in organizations}
    
    @staticmethod
    async def get_organization_metrics_optimized(
        org_id: str,
        date_range: tuple[datetime, datetime],
        db: AsyncSession
    ) -> Dict[str, Any]:
        """Get organization metrics with optimized queries."""
        
        # Use raw SQL for complex aggregations
        metrics_query = text("""
            SELECT 
                COUNT(DISTINCT wc.id) as total_conversations,
                COUNT(DISTINCT CASE WHEN wc.status = 'resolved' THEN wc.id END) as resolved_conversations,
                AVG(wc.satisfaction_rating) as avg_satisfaction,
                COUNT(DISTINCT agc.id) as generated_content_count,
                SUM(agc.generation_cost_cents) as total_ai_cost_cents
            FROM organizations o
            LEFT JOIN whatsapp_conversations wc ON wc.organization_id = o.id
                AND wc.created_at BETWEEN :start_date AND :end_date
            LEFT JOIN ai_generated_content agc ON agc.organization_id = o.id
                AND agc.created_at BETWEEN :start_date AND :end_date  
            WHERE o.id = :org_id
        """)
        
        result = await db.execute(
            metrics_query,
            {
                "org_id": org_id,
                "start_date": date_range[0],
                "end_date": date_range[1]
            }
        )
        
        return result.fetchone()._asdict()

# Caching strategies
import redis
from functools import wraps

redis_client = redis.Redis(host='localhost', port=6379, db=0)

def cache_result(expire_seconds: int = 300):
    """Cache function results in Redis."""
    
    def decorator(func: Callable) -> Callable:
        @wraps(func)
        async def wrapper(*args, **kwargs) -> Any:
            # Generate cache key from function name and arguments
            cache_key = f"{func.__name__}:{hash(str(args) + str(kwargs))}"
            
            # Try to get cached result
            cached_result = redis_client.get(cache_key)
            if cached_result:
                logger.debug("Cache hit", function=func.__name__, cache_key=cache_key)
                return json.loads(cached_result)
            
            # Execute function and cache result
            result = await func(*args, **kwargs)
            
            redis_client.setex(
                cache_key,
                expire_seconds, 
                json.dumps(result, default=str)
            )
            
            logger.debug("Cache miss", function=func.__name__, cache_key=cache_key)
            return result
            
        return wrapper
    return decorator

# API rate limiting
from fastapi import HTTPException, Request
import time

class RateLimiter:
    """Simple in-memory rate limiter for API endpoints."""
    
    def __init__(self):
        self.requests = {}
        
    def is_allowed(
        self, 
        key: str, 
        limit: int, 
        window_seconds: int
    ) -> bool:
        """Check if request is allowed under rate limit."""
        
        now = time.time()
        
        if key not in self.requests:
            self.requests[key] = []
        
        # Remove old requests outside the window
        self.requests[key] = [
            req_time for req_time in self.requests[key]
            if now - req_time < window_seconds
        ]
        
        # Check if under limit
        if len(self.requests[key]) >= limit:
            return False
        
        # Add current request
        self.requests[key].append(now)
        return True

rate_limiter = RateLimiter()

def rate_limit(requests_per_minute: int = 60):
    """Rate limiting decorator for API endpoints."""
    
    def decorator(func: Callable) -> Callable:
        @wraps(func)
        async def wrapper(request: Request, *args, **kwargs):
            # Use IP address as rate limiting key
            client_ip = request.client.host
            
            if not rate_limiter.is_allowed(
                key=f"rate_limit:{client_ip}",
                limit=requests_per_minute,
                window_seconds=60
            ):
                raise HTTPException(
                    status_code=429,
                    detail="Rate limit exceeded. Try again later."
                )
            
            return await func(request, *args, **kwargs)
            
        return wrapper
    return decorator

# Background task processing
from celery import Celery

celery_app = Celery('ai_departments_tasks')

@celery_app.task(bind=True, max_retries=3)
def process_ai_content_generation(
    self,
    organization_id: str,
    department_type: str,
    content_request: Dict[str, Any]
):
    """Process AI content generation as background task."""
    
    try:
        # Generate content using AI service
        content = generate_ai_content(
            organization_id=organization_id,
            department_type=department_type,
            request=content_request
        )
        
        # Save to database
        save_generated_content(content)
        
        # Notify user of completion
        notify_content_ready(organization_id, content.id)
        
        return {"success": True, "content_id": content.id}
        
    except Exception as exc:
        logger.error(
            "AI content generation failed",
            organization_id=organization_id,
            error=str(exc),
            retry_count=self.request.retries
        )
        
        if self.request.retries < 3:
            # Exponential backoff retry
            raise self.retry(countdown=60 * (2 ** self.request.retries))
        
        # Final failure - notify user
        notify_content_generation_failed(organization_id, str(exc))
        raise
```

---

## 9. Security Standards

### 9.1 Authentication & Authorization
```typescript
// security_standards.ts

// JWT Token Management
interface JWTPayload {
  sub: string; // User ID
  org: string; // Organization ID
  role: string; // User role
  iat: number; // Issued at
  exp: number; // Expires at
}

export class AuthenticationService {
  private static readonly TOKEN_EXPIRY = 15 * 60; // 15 minutes
  private static readonly REFRESH_TOKEN_EXPIRY = 7 * 24 * 60 * 60; // 7 days
  
  static generateTokens(user: User): { accessToken: string; refreshToken: string } {
    const payload: JWTPayload = {
      sub: user.id,
      org: user.organizationId,
      role: user.role,
      iat: Math.floor(Date.now() / 1000),
      exp: Math.floor(Date.now() / 1000) + this.TOKEN_EXPIRY
    };
    
    const accessToken = jwt.sign(payload, process.env.JWT_SECRET!);
    const refreshToken = this.generateRefreshToken(user.id);
    
    return { accessToken, refreshToken };
  }
  
  static validateToken(token: string): JWTPayload | null {
    try {
      return jwt.verify(token, process.env.JWT_SECRET!) as JWTPayload;
    } catch (error) {
      logger.warn('Invalid JWT token', { error: error.message });
      return null;
    }
  }
  
  static async refreshAccessToken(refreshToken: string): Promise<string | null> {
    // Validate refresh token against database
    const storedToken = await getStoredRefreshToken(refreshToken);
    if (!storedToken || storedToken.expiresAt < new Date()) {
      return null;
    }
    
    const user = await getUserById(storedToken.userId);
    if (!user) {
      return null;
    }
    
    const { accessToken } = this.generateTokens(user);
    return accessToken;
  }
}

// Input validation and sanitization
import { z } from 'zod';
import DOMPurify from 'isomorphic-dompurify';

// Brazilian-specific validation schemas
export const brazilianPhoneSchema = z.string().regex(
  /^\+55\s?\(?\d{2}\)?\s?\d{4,5}-?\d{4}$/,
  'Invalid Brazilian phone number'
);

export const cnpjSchema = z.string().regex(
  /^\d{2}\.\d{3}\.\d{3}\/\d{4}-\d{2}$/,
  'Invalid CNPJ format'
).refine(validateCNPJ, 'Invalid CNPJ number');

export const cepSchema = z.string().regex(
  /^\d{5}-?\d{3}$/,
  'Invalid CEP format'
);

// Input sanitization utility
export const sanitizeInput = {
  html: (input: string): string => {
    return DOMPurify.sanitize(input, { 
      ALLOWED_TAGS: ['b', 'i', 'em', 'strong'],
      ALLOWED_ATTR: []
    });
  },
  
  businessName: (input: string): string => {
    // Remove potentially harmful characters but keep accents
    return input.replace(/[<>\"\'&]/g, '').trim().substring(0, 100);
  },
  
  searchQuery: (input: string): string => {
    // Basic SQL injection prevention
    return input.replace(/[';\"\\]/g, '').trim().substring(0, 200);
  }
};

// CSRF Protection
export const csrfProtection = {
  generateToken: (): string => {
    return crypto.randomBytes(32).toString('hex');
  },
  
  validateToken: (submittedToken: string, sessionToken: string): boolean => {
    return submittedToken === sessionToken && submittedToken.length === 64;
  }
};
```

### 9.2 Data Protection & LGPD Compliance
```python
# data_protection.py - LGPD compliance utilities

from enum import Enum
from typing import List, Optional, Dict, Any
import hashlib
import secrets
from datetime import datetime, timedelta

class DataClassification(Enum):
    """Data classification levels for LGPD compliance."""
    PUBLIC = "public"
    INTERNAL = "internal"
    CONFIDENTIAL = "confidential"  
    RESTRICTED = "restricted"      # Personal data
    SENSITIVE = "sensitive"        # Sensitive personal data

class PersonalDataProcessor:
    """Handle personal data processing according to LGPD requirements."""
    
    @staticmethod
    def hash_cpf(cpf: str) -> str:
        """Hash CPF for storage while maintaining uniqueness."""
        salt = "ai_departments_cpf_salt"  # Should be in environment
        return hashlib.sha256(f"{cpf}{salt}".encode()).hexdigest()
    
    @staticmethod
    def mask_phone(phone: str) -> str:
        """Mask phone number for display purposes."""
        if len(phone) >= 10:
            return f"{phone[:2]}***{phone[-4:]}"
        return "***"
    
    @staticmethod
    def mask_email(email: str) -> str:
        """Mask email address for display purposes."""
        local, domain = email.split('@')
        if len(local) <= 2:
            masked_local = '*' * len(local)
        else:
            masked_local = local[0] + '*' * (len(local) - 2) + local[-1]
        return f"{masked_local}@{domain}"
    
    @staticmethod
    def anonymize_customer_data(customer: Dict[str, Any]) -> Dict[str, Any]:
        """Anonymize customer data for analytics while preserving utility."""
        return {
            "id": customer["id"],  # Keep ID for relationships
            "business_type": customer.get("business_type"),
            "industry": customer.get("industry"), 
            "location_state": customer.get("state"),  # State only, not full address
            "created_month": customer["created_at"][:7],  # YYYY-MM only
            "subscription_plan": customer.get("subscription_plan"),
            # Remove all personally identifiable information
        }

class DataRetentionManager:
    """Manage data retention periods according to LGPD."""
    
    # LGPD retention periods by data type
    RETENTION_PERIODS = {
        "customer_data": timedelta(days=5 * 365),      # 5 years
        "transaction_records": timedelta(days=7 * 365), # 7 years (tax requirement)
        "marketing_data": timedelta(days=2 * 365),      # 2 years
        "support_conversations": timedelta(days=3 * 365), # 3 years
        "audit_logs": timedelta(days=10 * 365),         # 10 years
        "ai_generated_content": timedelta(days=1 * 365), # 1 year
    }
    
    @classmethod
    async def schedule_data_deletion(
        cls,
        organization_id: str,
        data_type: str,
        created_at: datetime
    ) -> None:
        """Schedule data for deletion according to retention policy."""
        
        retention_period = cls.RETENTION_PERIODS.get(data_type)
        if not retention_period:
            raise ValueError(f"Unknown data type: {data_type}")
        
        deletion_date = created_at + retention_period
        
        # Schedule background task for deletion
        await schedule_data_deletion_task(
            organization_id=organization_id,
            data_type=data_type,
            deletion_date=deletion_date
        )
    
    @classmethod
    async def handle_data_subject_deletion_request(
        cls,
        user_id: str,
        organization_id: str
    ) -> Dict[str, Any]:
        """Handle LGPD data subject deletion request."""
        
        deleted_data = {
            "personal_data": [],
            "retained_data": [],
            "anonymized_data": []
        }
        
        # Delete personal data that can be deleted
        personal_data_tables = [
            "users", "user_profiles", "whatsapp_conversations",
            "support_tickets", "marketing_preferences"
        ]
        
        for table in personal_data_tables:
            count = await delete_user_data_from_table(table, user_id)
            deleted_data["personal_data"].append({
                "table": table,
                "records_deleted": count
            })
        
        # Retain data required by law (anonymized)
        legal_retention_tables = [
            "business_transactions", "tax_documents", "invoices"
        ]
        
        for table in legal_retention_tables:
            count = await anonymize_user_data_in_table(table, user_id)
            deleted_data["anonymized_data"].append({
                "table": table,
                "records_anonymized": count
            })
        
        # Log the deletion request for audit
        await log_data_deletion_request(
            user_id=user_id,
            organization_id=organization_id,
            deletion_summary=deleted_data,
            processed_at=datetime.utcnow()
        )
        
        return deleted_data

class LGPDConsentManager:
    """Manage consent for data processing according to LGPD."""
    
    @staticmethod
    async def record_consent(
        user_id: str,
        consent_type: str,
        purpose: str,
        legal_basis: str,
        granted: bool
    ) -> str:
        """Record user consent with full audit trail."""
        
        consent_record = {
            "id": secrets.token_urlsafe(32),
            "user_id": user_id,
            "consent_type": consent_type,
            "purpose": purpose,
            "legal_basis": legal_basis,  # Art. 7 LGPD basis
            "granted": granted,
            "recorded_at": datetime.utcnow(),
            "ip_address": get_client_ip(),  # For audit trail
            "user_agent": get_user_agent()
        }
        
        await save_consent_record(consent_record)
        
        logger.info(
            "Consent recorded",
            user_id=user_id,
            consent_type=consent_type,
            granted=granted
        )
        
        return consent_record["id"]
    
    @staticmethod
    async def check_consent(
        user_id: str,
        consent_type: str
    ) -> Optional[Dict[str, Any]]:
        """Check if user has granted consent for specific processing."""
        
        return await get_latest_consent(user_id, consent_type)
    
    @staticmethod
    async def withdraw_consent(
        user_id: str,
        consent_type: str
    ) -> None:
        """Handle consent withdrawal with immediate effect."""
        
        # Record consent withdrawal
        await record_consent(
            user_id=user_id,
            consent_type=consent_type,
            purpose="consent_withdrawal",
            legal_basis="art_7_lgpd",
            granted=False
        )
        
        # Immediately stop processing based on withdrawn consent
        await stop_data_processing_for_consent_type(user_id, consent_type)
        
        logger.info(
            "Consent withdrawn",
            user_id=user_id,
            consent_type=consent_type
        )

# Data encryption utilities
import cryptography
from cryptography.fernet import Fernet

class DataEncryption:
    """Handle encryption for sensitive data."""
    
    def __init__(self):
        # In production, this would come from secure key management
        self.encryption_key = os.environ.get('DATA_ENCRYPTION_KEY')
        if not self.encryption_key:
            raise ValueError("DATA_ENCRYPTION_KEY environment variable required")
        
        self.cipher_suite = Fernet(self.encryption_key.encode())
    
    def encrypt_sensitive_data(self, data: str) -> str:
        """Encrypt sensitive data for storage."""
        return self.cipher_suite.encrypt(data.encode()).decode()
    
    def decrypt_sensitive_data(self, encrypted_data: str) -> str:
        """Decrypt sensitive data for use."""
        return self.cipher_suite.decrypt(encrypted_data.encode()).decode()
    
    def encrypt_pii_fields(self, record: Dict[str, Any]) -> Dict[str, Any]:
        """Encrypt PII fields in a database record."""
        
        pii_fields = ['cpf', 'full_address', 'phone', 'email']
        encrypted_record = record.copy()
        
        for field in pii_fields:
            if field in encrypted_record and encrypted_record[field]:
                encrypted_record[field] = self.encrypt_sensitive_data(
                    str(encrypted_record[field])
                )
        
        return encrypted_record
```

---

## 10. Documentation Standards

### 10.1 Code Documentation
```typescript
// documentation_standards.ts

/**
 * Brazilian Business Profile Form Component
 * 
 * A comprehensive form for collecting and validating Brazilian business information
 * including CNPJ validation, CEP auto-completion, and LGPD-compliant data handling.
 * 
 * @component
 * @example
 * ```tsx
 * <BrazilianBusinessProfileForm
 *   initialData={existingProfile}
 *   onSubmit={handleProfileSubmit}
 *   onCancel={() => router.back()}
 * />
 * ```
 * 
 * @param props - Component props
 * @param props.initialData - Pre-populate form with existing data
 * @param props.onSubmit - Callback when form is successfully submitted
 * @param props.onCancel - Optional callback when form is cancelled
 * 
 * @returns JSX element representing the business profile form
 * 
 * @throws {ValidationError} When form data doesn't meet Brazilian business requirements
 * 
 * @since 1.0.0
 * @author AI Departments Platform Team
 * 
 * @see {@link https://docs.aidepartments.com.br/forms/business-profile}
 * 
 * @remarks
 * This component handles Brazilian-specific business data including:
 * - CNPJ validation using official algorithm
 * - CEP auto-completion via ViaCEP API
 * - Phone number formatting for Brazilian numbers
 * - LGPD-compliant data collection consent
 * 
 * The form automatically saves data to localStorage for recovery in case of
 * browser crashes or accidental navigation.
 */
export const BrazilianBusinessProfileForm: React.FC<BrazilianBusinessProfileProps> = ({
  initialData,
  onSubmit,
  onCancel
}) => {
  // Component implementation...
};

/**
 * Validates a Brazilian CNPJ number using the official algorithm
 * 
 * @param cnpj - The CNPJ number to validate (can be formatted or unformatted)
 * @returns True if the CNPJ is valid, false otherwise
 * 
 * @example
 * ```typescript
 * validateCNPJ('11.222.333/0001-81'); // returns true
 * validateCNPJ('11222333000181');     // returns true  
 * validateCNPJ('11.222.333/0001-80'); // returns false
 * ```
 * 
 * @remarks
 * This function implements the official CNPJ validation algorithm used by
 * the Brazilian Federal Revenue Service. It validates both the format and
 * the check digits.
 * 
 * @see {@link https://www.receita.fazenda.gov.br/} Brazilian Federal Revenue Service
 */
export function validateCNPJ(cnpj: string): boolean {
  // Remove all non-numeric characters
  const digits = cnpj.replace(/\D/g, '');
  
  // CNPJ must have exactly 14 digits
  if (digits.length !== 14) {
    return false;
  }
  
  // Check for known invalid CNPJs (all same digit)
  if (/^(\d)\1{13}$/.test(digits)) {
    return false;
  }
  
  // Validate first check digit
  let sum = 0;
  let weight = 5;
  
  for (let i = 0; i < 12; i++) {
    sum += parseInt(digits[i]) * weight;
    weight = weight === 2 ? 9 : weight - 1;
  }
  
  const remainder = sum % 11;
  const firstCheckDigit = remainder < 2 ? 0 : 11 - remainder;
  
  if (parseInt(digits[12]) !== firstCheckDigit) {
    return false;
  }
  
  // Validate second check digit
  sum = 0;
  weight = 6;
  
  for (let i = 0; i < 13; i++) {
    sum += parseInt(digits[i]) * weight;
    weight = weight === 2 ? 9 : weight - 1;
  }
  
  const secondRemainder = sum % 11;
  const secondCheckDigit = secondRemainder < 2 ? 0 : 11 - secondRemainder;
  
  return parseInt(digits[13]) === secondCheckDigit;
}
```

### 10.2 API Documentation
```yaml
# api_documentation_standards.yaml
openapi_documentation:
  info:
    title: "AI Departments Platform API"
    version: "1.0.0"
    description: |
      Comprehensive API for managing AI-powered business departments for Brazilian micro-entrepreneurs.
      
      ## Authentication
      All endpoints require authentication using JWT tokens in the Authorization header:
      ```
      Authorization: Bearer <your_jwt_token>
      ```
      
      ## Rate Limiting
      API requests are limited to 60 requests per minute per IP address.
      
      ## Brazilian Localization
      - All dates are in ISO 8601 format with America/Sao_Paulo timezone
      - Currency values are in Brazilian Real (BRL) cents
      - Phone numbers follow Brazilian format (+55 XX XXXXX-XXXX)
      - Address data includes Brazilian CEP and state codes
    
    contact:
      name: "API Support"
      url: "https://docs.aidepartments.com.br/support"
      email: "api-support@aidepartments.com.br"
    
    license:
      name: "Proprietary"
      url: "https://aidepartments.com.br/terms"

  servers:
    - url: "https://api.aidepartments.com.br/v1"
      description: "Production server"
    - url: "https://staging-api.aidepartments.com.br/v1"  
      description: "Staging server"
    - url: "http://localhost:8000/v1"
      description: "Local development server"

  paths:
    "/organizations":
      post:
        summary: "Create a new Brazilian business organization"
        description: |
          Creates a new organization profile for a Brazilian business, including
          validation of Brazilian-specific data like CNPJ, CEP, and phone numbers.
          
          This endpoint automatically:
          - Validates CNPJ using the official algorithm
          - Enriches address data using CEP lookup
          - Sets up default departments for Brazilian market
          - Configures timezone and currency for Brazil
          
        tags: ["Organizations"]
        requestBody:
          required: true
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/BrazilianBusinessProfile"
              example:
                businessName: "Boutique da Maria"
                cnpj: "12.345.678/0001-95"
                phone: "+55 (11) 99988-7766"
                email: "maria@boutique.com.br"
                address:
                  cep: "01234-567"
                  street: "Rua das Flores"
                  number: "123"
                  neighborhood: "Vila Madalena"
                  city: "São Paulo"
                  state: "SP"
                  complement: "Loja 2"
                businessType: "MEI"
                industry: "fashion_retail"
        
        responses:
          "201":
            description: "Organization created successfully"
            content:
              application/json:
                schema:
                  type: object
                  properties:
                    success:
                      type: boolean
                      example: true
                    data:
                      $ref: "#/components/schemas/Organization"
                    message:
                      type: string
                      example: "Organização criada com sucesso"
          
          "400":
            description: "Invalid input data"
            content:
              application/json:
                schema:
                  $ref: "#/components/schemas/ErrorResponse"
                example:
                  success: false
                  error:
                    code: "VALIDATION_ERROR"
                    message: "CNPJ inválido"
                    details:
                      field: "cnpj"
                      reason: "CNPJ check digits are incorrect"
          
          "409":
            description: "Organization already exists"
            content:
              application/json:
                schema:
                  $ref: "#/components/schemas/ErrorResponse"
                example:
                  success: false
                  error:
                    code: "DUPLICATE_CNPJ"
                    message: "CNPJ já cadastrado no sistema"

  components:
    schemas:
      BrazilianBusinessProfile:
        type: object
        required:
          - businessName
          - phone
          - email
          - address
          - businessType
          - industry
        properties:
          businessName:
            type: string
            minLength: 2
            maxLength: 100
            description: "Legal business name"
            example: "Boutique da Maria"
          
          cnpj:
            type: string
            pattern: "^\\d{2}\\.\\d{3}\\.\\d{3}\\/\\d{4}-\\d{2}$"
            description: "Brazilian CNPJ number in formatted form (optional for MEI)"
            example: "12.345.678/0001-95"
          
          phone:
            type: string
            pattern: "^\\+55\\s\\(\\d{2}\\)\\s\\d{4,5}-\\d{4}$"
            description: "Brazilian phone number in international format"
            example: "+55 (11) 99988-7766"
          
          email:
            type: string
            format: email
            description: "Business email address"
            example: "maria@boutique.com.br"
          
          address:
            $ref: "#/components/schemas/BrazilianAddress"
          
          businessType:
            type: string
            enum: ["MEI", "MICRO_ENTERPRISE", "SMALL_BUSINESS", "MEDIUM_BUSINESS"]
            description: "Brazilian business classification"
            example: "MEI"
          
          industry:
            type: string
            description: "Business industry/sector"
            example: "fashion_retail"
      
      BrazilianAddress:
        type: object
        required:
          - cep
          - street
          - number
          - neighborhood
          - city
          - state
        properties:
          cep:
            type: string
            pattern: "^\\d{5}-\\d{3}$"
            description: "Brazilian postal code (CEP)"
            example: "01234-567"
          
          street:
            type: string
            description: "Street name"
            example: "Rua das Flores"
          
          number:
            type: string
            description: "Street number"
            example: "123"
          
          neighborhood:
            type: string
            description: "Neighborhood name"
            example: "Vila Madalena"
          
          city:
            type: string
            description: "City name"
            example: "São Paulo"
          
          state:
            type: string
            pattern: "^[A-Z]{2}$"
            description: "Brazilian state code (2 letters)"
            example: "SP"
          
          complement:
            type: string
            description: "Additional address information (apartment, suite, etc.)"
            example: "Loja 2"

    securitySchemes:
      BearerAuth:
        type: http
        scheme: bearer
        bearerFormat: JWT
        description: "JWT token obtained from /auth/login endpoint"

  security:
    - BearerAuth: []
```

---

**Document Status:** Comprehensive coding standards ready for development team adoption and continuous improvement. These standards ensure code quality, performance, security, and maintainability across the entire AI Departments Platform.