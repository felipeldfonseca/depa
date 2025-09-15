# Load Testing & Performance Engineering

## Overview
Comprehensive load testing strategy for the AI Departments Platform to ensure scalability and performance under various load conditions while maintaining cost efficiency for a startup environment.

## 1. Performance Requirements

### 1.1 Response Time SLAs
```yaml
# Performance SLAs by Service Tier
response_times:
  gemini_flash_requests: "< 800ms p95"
  api_endpoints: "< 500ms p95"  
  database_queries: "< 100ms p95"
  file_uploads: "< 2s p95"
  whatsapp_webhook: "< 200ms p95"

throughput:
  concurrent_users: 1000
  requests_per_second: 500
  ai_requests_per_minute: 1000
  database_transactions: 2000/min
```

### 1.2 Scalability Targets
```yaml
# Growth-oriented scaling targets
scaling_targets:
  month_1: 100 concurrent users
  month_6: 500 concurrent users  
  month_12: 1000 concurrent users
  month_24: 5000 concurrent users

resource_efficiency:
  cost_per_user_per_month: "< R$ 12"
  infrastructure_cost_ratio: "< 30% of revenue"
  ai_cost_ratio: "< 20% of revenue"
```

## 2. Load Testing Architecture

### 2.1 Testing Stack
```python
# load_test_stack.py
class LoadTestStack:
    def __init__(self):
        self.primary_tool = "Locust"  # Open source, Python-based
        self.monitoring = "Grafana + Prometheus"
        self.database_testing = "pgbench"
        self.ai_testing = "Custom AI Load Simulator"
        
    def cost_efficient_setup(self):
        return {
            "cloud_provider": "Google Cloud",
            "test_instances": "e2-medium (2 vCPU, 4GB)",
            "estimated_cost": "R$ 50/month for continuous testing",
            "scaling": "Auto-scale test runners based on demand"
        }
```

### 2.2 Test Environment Architecture
```yaml
# test_environment.yaml
test_environments:
  staging_load:
    infrastructure: "75% of production capacity"
    database: "Cloud SQL (db-n1-standard-2)"
    redis: "M3 instance (2.5GB memory)"
    ai_quotas: "50% of production limits"
    
  production_shadow:
    type: "Shadow traffic testing"
    traffic_percentage: "5% of production"
    real_data: false
    ai_models: "Same as production"
```

## 3. Load Testing Scenarios

### 3.1 Core User Journeys
```python
# core_scenarios.py
from locust import HttpUser, task, between
import random
import json

class BrazilianMicroEntrepreneurUser(HttpUser):
    wait_time = between(2, 8)  # Realistic user behavior
    
    def on_start(self):
        """Setup user session"""
        self.client.post("/auth/login", json={
            "email": f"user_{random.randint(1000,9999)}@gmail.com",
            "password": "TestPass123"
        })
        
    @task(30)  # Most common scenario
    def create_marketing_content(self):
        """Generate marketing content using Gemini Flash"""
        business_types = ["padaria", "restaurante", "loja_roupas", "salao_beleza"]
        business = random.choice(business_types)
        
        response = self.client.post("/api/v1/departments/marketing/generate", json={
            "business_type": business,
            "content_type": "instagram_post",
            "model": "gemini-2.5-flash",  # Cost-efficient default
            "context": f"Negócio: {business} em São Paulo"
        })
        
        if response.status_code == 200:
            self.check_content_quality(response.json())
            
    @task(20)
    def customer_service_chat(self):
        """WhatsApp customer service simulation"""
        messages = [
            "Olá, preciso de ajuda",
            "Qual o horário de funcionamento?",
            "Vocês fazem entrega?",
            "Quanto custa o produto X?"
        ]
        
        self.client.post("/api/v1/departments/cx/chat", json={
            "message": random.choice(messages),
            "channel": "whatsapp",
            "business_id": f"biz_{random.randint(100,999)}"
        })
        
    @task(15)
    def business_setup_wizard(self):
        """New business onboarding flow"""
        self.client.post("/api/v1/business/setup", json={
            "business_name": f"Empresa Test {random.randint(1,1000)}",
            "business_type": "restaurant",
            "location": "São Paulo, SP",
            "template": "restaurant_basic"
        })
        
    @task(10)
    def file_upload_scenario(self):
        """Logo/image upload testing"""
        files = {'file': ('test_logo.png', b'fake_png_data', 'image/png')}
        self.client.post("/api/v1/upload/logo", files=files)
        
    def check_content_quality(self, content_data):
        """Validate AI-generated content quality"""
        required_fields = ["content", "business_relevance_score"]
        assert all(field in content_data for field in required_fields)
        assert content_data["business_relevance_score"] >= 0.7

class AIModelLoadTester(HttpUser):
    """Specialized AI model performance testing"""
    wait_time = between(1, 3)
    
    @task
    def gemini_flash_load_test(self):
        """Test Gemini 2.5 Flash under load"""
        prompts = [
            "Crie um post para Instagram de uma padaria",
            "Escreva uma resposta para reclamação de cliente",
            "Gere ideias de promoção para restaurante",
            "Crie descrição de produto para e-commerce"
        ]
        
        start_time = time.time()
        response = self.client.post("/api/v1/ai/generate", json={
            "prompt": random.choice(prompts),
            "model": "gemini-2.5-flash",
            "max_tokens": 300
        })
        end_time = time.time()
        
        # Track AI-specific metrics
        self.environment.events.request_success.fire(
            request_type="AI",
            name="gemini_flash_generation",
            response_time=(end_time - start_time) * 1000,
            response_length=len(response.text)
        )
```

### 3.2 Brazilian Business Context Testing
```python
# brazilian_context_tests.py
class BrazilianBusinessContextTester(HttpUser):
    """Test platform performance with Brazilian business context"""
    
    @task
    def test_regional_content_generation(self):
        """Test content generation for different Brazilian regions"""
        regions = ["São Paulo", "Rio de Janeiro", "Belo Horizonte", "Salvador", "Recife"]
        business_types = ["açaí", "churrascaria", "farmácia", "pet shop", "academia"]
        
        for region in regions:
            for business in business_types:
                self.client.post("/api/v1/departments/marketing/generate", json={
                    "business_type": business,
                    "location": f"{region}, Brasil",
                    "content_type": "social_media_post",
                    "model": "gemini-2.5-flash"
                })
                
    @task  
    def test_lgpd_compliance_load(self):
        """Test LGPD compliance under load"""
        self.client.post("/api/v1/privacy/data-request", json={
            "request_type": "deletion",
            "user_id": f"user_{random.randint(1000,9999)}",
            "business_id": f"biz_{random.randint(100,999)}"
        })
```

## 4. Cost-Efficient Testing Strategy

### 4.1 Testing Budget Optimization
```yaml
# cost_optimization.yaml
testing_budget:
  monthly_budget: "R$ 500"
  allocation:
    cloud_instances: "60% (R$ 300)"
    ai_testing_quota: "30% (R$ 150)"
    monitoring_tools: "10% (R$ 50)"
    
cost_controls:
  auto_shutdown: "Tests stop after 2 hours"
  scheduled_testing: "Off-peak hours (2AM-6AM UTC-3)"
  resource_limits: "Max 5 test instances"
  ai_rate_limits: "100 requests/minute during testing"
```

### 4.2 Smart Testing Approach
```python
# smart_testing.py
class CostOptimizedLoadTesting:
    def __init__(self):
        self.test_schedule = {
            "daily_quick": "10 minutes, 50 users",
            "weekly_medium": "1 hour, 200 users", 
            "monthly_full": "4 hours, 1000 users",
            "pre_release": "2 hours, 500 users"
        }
        
    def adaptive_testing(self, performance_metrics):
        """Adjust test intensity based on results"""
        if performance_metrics["error_rate"] > 0.1:
            return "increase_test_duration"
        elif performance_metrics["response_time_p95"] > 800:
            return "add_stress_scenarios"
        else:
            return "reduce_test_scope"  # Save costs
            
    def ai_model_rotation_testing(self):
        """Test different AI models to validate cost efficiency"""
        models = [
            {"name": "gemini-2.5-flash", "cost": 0.001, "weight": 80},
            {"name": "gpt-3.5-turbo", "cost": 0.002, "weight": 15},
            {"name": "gpt-4-turbo", "cost": 0.030, "weight": 5}
        ]
        
        for model in models:
            load_percentage = model["weight"]
            self.run_model_specific_test(model["name"], load_percentage)
```

## 5. Database Load Testing

### 5.1 PostgreSQL Performance Testing
```sql
-- pgbench_custom_scripts.sql
-- Test multi-tenant query performance
\set business_id random(1, 1000)
\set user_id random(1, 10000)

SELECT * FROM businesses b
JOIN users u ON b.id = u.business_id  
WHERE b.id = :business_id
AND b.status = 'active';

-- Test AI generation history queries
SELECT * FROM ai_generations ag
WHERE ag.business_id = :business_id
AND ag.created_at > NOW() - INTERVAL '7 days'
ORDER BY ag.created_at DESC
LIMIT 50;

-- Test WhatsApp message throughput
INSERT INTO whatsapp_messages (business_id, user_id, content, direction)
VALUES (:business_id, :user_id, 'Test message content', 'inbound');
```

### 5.2 Database Performance Benchmarks
```bash
#!/bin/bash
# database_load_tests.sh

# Basic pgbench performance test
pgbench -h $DB_HOST -U $DB_USER -d $DB_NAME \
  -c 50 -j 4 -T 300 -P 10 \
  --builtin=tpcb-like

# Custom business scenario testing  
pgbench -h $DB_HOST -U $DB_USER -d $DB_NAME \
  -c 50 -j 4 -T 600 -P 30 \
  -f pgbench_custom_scripts.sql

# Connection pool stress testing
pgbench -h $DB_HOST -U $DB_USER -d $DB_NAME \
  -c 100 -j 8 -T 300 \
  --builtin=simple-update
```

## 6. AI Load Testing Framework

### 6.1 AI-Specific Metrics
```python
# ai_load_metrics.py
class AILoadTestingMetrics:
    def __init__(self):
        self.metrics = {
            "ai_request_latency": [],
            "token_generation_rate": [],
            "model_switching_time": [],
            "content_quality_scores": [],
            "cost_per_request": []
        }
        
    def track_ai_performance(self, model_name, request_data, response_data):
        """Track AI-specific performance metrics"""
        cost_per_token = {
            "gemini-2.5-flash": 0.000001,  # Very cost efficient
            "gpt-3.5-turbo": 0.000002,
            "gpt-4-turbo": 0.00003
        }
        
        tokens_used = response_data.get("usage", {}).get("total_tokens", 0)
        cost = tokens_used * cost_per_token.get(model_name, 0)
        
        self.metrics["cost_per_request"].append(cost)
        self.metrics["content_quality_scores"].append(
            self.assess_content_quality(response_data)
        )
        
    def assess_content_quality(self, response_data):
        """Basic content quality assessment"""
        content = response_data.get("content", "")
        quality_score = 0
        
        # Brazilian Portuguese check
        if any(word in content.lower() for word in ["você", "nosso", "empresa"]):
            quality_score += 0.3
            
        # Business relevance check  
        if len(content.split()) >= 20:  # Minimum content length
            quality_score += 0.4
            
        # No obvious errors
        if "error" not in content.lower():
            quality_score += 0.3
            
        return quality_score

class AIStressTestRunner:
    def __init__(self):
        self.concurrent_requests = 50
        self.test_duration = 1800  # 30 minutes
        
    async def run_ai_stress_test(self):
        """Run concurrent AI requests to test limits"""
        tasks = []
        for i in range(self.concurrent_requests):
            task = asyncio.create_task(
                self.generate_ai_content_continuously()
            )
            tasks.append(task)
            
        await asyncio.gather(*tasks)
        
    async def generate_ai_content_continuously(self):
        """Generate AI content continuously during test"""
        start_time = time.time()
        request_count = 0
        
        while time.time() - start_time < self.test_duration:
            try:
                await self.make_ai_request()
                request_count += 1
                await asyncio.sleep(random.uniform(1, 3))
            except Exception as e:
                logging.error(f"AI request failed: {e}")
                
        logging.info(f"Completed {request_count} AI requests")
```

## 7. Monitoring & Alerting

### 7.1 Performance Monitoring Setup
```yaml
# monitoring_config.yaml
monitoring:
  grafana_dashboards:
    - "API Performance"
    - "Database Performance"  
    - "AI Model Performance"
    - "Cost Tracking"
    - "User Experience Metrics"
    
  prometheus_metrics:
    - http_request_duration_seconds
    - database_query_duration_seconds
    - ai_generation_duration_seconds
    - cost_per_ai_request
    - business_activation_rate
    
  alerting_rules:
    - name: "High Response Time"
      condition: "p95 > 1000ms for 5 minutes"
      severity: "warning"
    - name: "AI Cost Spike" 
      condition: "hourly_ai_cost > R$ 50"
      severity: "critical"
    - name: "Database Slow Queries"
      condition: "query_time > 500ms"  
      severity: "warning"
```

### 7.2 Cost Monitoring
```python
# cost_monitoring.py
class CostMonitoringSystem:
    def __init__(self):
        self.cost_limits = {
            "daily_ai_cost": 200.0,   # R$ 200/day
            "hourly_peak_cost": 50.0, # R$ 50/hour
            "user_acquisition_cost": 25.0  # R$ 25 per user
        }
        
    def track_performance_costs(self):
        """Monitor costs during load testing"""
        current_costs = {
            "infrastructure": self.get_gcp_costs(),
            "ai_models": self.get_ai_model_costs(),
            "data_storage": self.get_storage_costs()
        }
        
        total_cost = sum(current_costs.values())
        
        if total_cost > self.cost_limits["hourly_peak_cost"]:
            self.trigger_cost_alert(total_cost)
            
    def optimize_test_costs(self):
        """Auto-optimize testing costs"""
        return {
            "reduce_test_instances": True,
            "switch_to_gemini_flash_only": True,
            "limit_concurrent_users": 200,
            "reduce_test_duration": "50%"
        }
```

## 8. Load Testing Pipeline

### 8.1 Automated Testing Schedule
```yaml
# load_test_pipeline.yaml
pipeline:
  triggers:
    - "daily: 3:00 AM UTC-3"
    - "pre_deploy: on PR merge"
    - "manual: on demand"
    
  test_stages:
    smoke_test:
      duration: "5 minutes"
      users: 10
      scenarios: ["basic_api", "auth_flow"]
      
    load_test:
      duration: "30 minutes" 
      users: 200
      scenarios: ["marketing_content", "customer_service"]
      
    stress_test:
      duration: "60 minutes"
      users: 500
      scenarios: ["all_departments", "file_uploads"]
      
    endurance_test:
      duration: "4 hours"
      users: 100
      scenarios: ["continuous_ai_generation"]
```

### 8.2 CI/CD Integration
```bash
#!/bin/bash
# load_test_pipeline.sh

set -e

echo "Starting Load Testing Pipeline..."

# Setup test environment
gcloud run deploy business-ai-load-test \
  --source . \
  --region=us-central1 \
  --memory=2Gi \
  --cpu=1

# Run performance tests
python -m pytest load_tests/ \
  --junitxml=load_test_results.xml \
  --html=load_test_report.html

# Generate performance report
locust --headless \
  --users 200 \
  --spawn-rate 10 \
  --run-time 30m \
  --host https://staging-api.businessai.com.br \
  --html load_test_report.html

# Check performance thresholds
python check_performance_thresholds.py

echo "Load Testing Pipeline Completed"
```

## 9. Performance Optimization Guidelines

### 9.1 Response Time Optimization
```python
# performance_optimizations.py
class PerformanceOptimizer:
    def __init__(self):
        self.optimizations = {
            "database": [
                "Add indexes on frequently queried columns",
                "Implement connection pooling",
                "Use read replicas for read-heavy operations"
            ],
            "api": [
                "Implement response caching",
                "Use async/await for I/O operations", 
                "Add request rate limiting"
            ],
            "ai_models": [
                "Cache frequently generated content",
                "Implement smart model routing",
                "Use streaming for long responses"
            ]
        }
        
    def auto_optimize_based_on_load_test_results(self, results):
        """Automatic optimization suggestions"""
        recommendations = []
        
        if results["db_query_time_p95"] > 100:
            recommendations.append("Add database indexes")
            
        if results["ai_response_time_p95"] > 800:
            recommendations.append("Implement AI response caching")
            
        if results["error_rate"] > 0.05:
            recommendations.append("Add circuit breaker pattern")
            
        return recommendations
```

### 9.2 Cost Performance Optimization
```python
# cost_performance_balance.py
class CostPerformanceOptimizer:
    def __init__(self):
        self.performance_cost_matrix = {
            "gemini-2.5-flash": {"cost": 1, "performance": 8, "quality": 7},
            "gpt-3.5-turbo": {"cost": 2, "performance": 9, "quality": 8},
            "gpt-4-turbo": {"cost": 30, "performance": 10, "quality": 10}
        }
        
    def optimize_model_selection_based_on_load(self, current_load):
        """Dynamic model selection based on load and cost"""
        if current_load < 100:  # Low load
            return "gemini-2.5-flash"  # Most cost efficient
        elif current_load < 500:  # Medium load
            return "mixed_routing"  # 80% Flash, 20% GPT-3.5
        else:  # High load
            return "gemini-2.5-flash"  # Stick to cost efficiency under pressure
            
    def calculate_cost_per_successful_interaction(self, test_results):
        """Calculate true cost efficiency including failed requests"""
        total_requests = test_results["total_requests"]
        successful_requests = test_results["successful_requests"]
        total_cost = test_results["total_ai_cost"]
        
        cost_per_successful_interaction = total_cost / successful_requests
        return cost_per_successful_interaction
```

## 10. Reporting & Analytics

### 10.1 Load Test Reports
```python
# load_test_reporting.py
class LoadTestReporter:
    def __init__(self):
        self.report_sections = [
            "executive_summary",
            "performance_metrics", 
            "cost_analysis",
            "scalability_assessment",
            "recommendations"
        ]
        
    def generate_comprehensive_report(self, test_results):
        """Generate comprehensive load test report"""
        report = {
            "executive_summary": {
                "test_date": datetime.now().isoformat(),
                "test_duration": test_results["duration"],
                "peak_users": test_results["peak_concurrent_users"],
                "overall_performance": self.calculate_performance_grade(test_results)
            },
            "performance_metrics": {
                "response_times": test_results["response_times"],
                "throughput": test_results["requests_per_second"],
                "error_rates": test_results["error_rates"],
                "ai_performance": test_results["ai_metrics"]
            },
            "cost_analysis": {
                "total_test_cost": test_results["total_cost"],
                "cost_per_user": test_results["cost_per_user"],
                "projected_monthly_cost": self.project_monthly_costs(test_results)
            },
            "scalability_assessment": {
                "current_capacity": test_results["max_stable_users"],
                "bottlenecks": test_results["identified_bottlenecks"],
                "scaling_recommendations": self.generate_scaling_plan(test_results)
            }
        }
        
        return report
        
    def calculate_performance_grade(self, results):
        """Calculate overall performance grade A-F"""
        score = 0
        
        # Response time score (40%)
        if results["api_response_time_p95"] < 500:
            score += 40
        elif results["api_response_time_p95"] < 1000:
            score += 30
        elif results["api_response_time_p95"] < 2000:
            score += 20
        else:
            score += 10
            
        # Error rate score (30%)
        if results["error_rate"] < 0.01:
            score += 30
        elif results["error_rate"] < 0.05:
            score += 20
        elif results["error_rate"] < 0.10:
            score += 10
        
        # Cost efficiency score (30%)
        if results["cost_per_user_per_hour"] < 0.05:
            score += 30
        elif results["cost_per_user_per_hour"] < 0.10:
            score += 20
        elif results["cost_per_user_per_hour"] < 0.20:
            score += 10
            
        if score >= 90: return "A"
        elif score >= 80: return "B"  
        elif score >= 70: return "C"
        elif score >= 60: return "D"
        else: return "F"
```

## 11. Implementation Timeline

### 11.1 Load Testing Rollout Plan
```yaml
# implementation_timeline.yaml
phase_1: # Weeks 1-2
  - "Setup basic Locust framework"
  - "Implement core user journey tests"
  - "Configure basic monitoring"
  
phase_2: # Weeks 3-4  
  - "Add AI-specific load testing"
  - "Implement cost monitoring"
  - "Setup automated test pipeline"
  
phase_3: # Weeks 5-6
  - "Add Brazilian context testing"
  - "Implement performance optimization recommendations"
  - "Complete reporting dashboard"
  
phase_4: # Weeks 7-8
  - "Production shadow testing"
  - "Performance tuning based on results"
  - "Documentation and training"
```

This comprehensive load testing framework ensures the AI Departments Platform can handle expected Brazilian micro-entrepreneur user loads while maintaining cost efficiency and high performance. The focus on Gemini 2.5 Flash as the primary AI model keeps costs low during testing while validating real-world performance scenarios.