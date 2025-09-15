# Safety & Brand Guardrails

## Overview
Comprehensive content moderation and brand safety framework for AI-generated content in the Brazilian micro-entrepreneur context, ensuring appropriate, safe, and brand-aligned outputs while maintaining cost efficiency.

## 1. Content Safety Framework

### 1.1 Safety Categories & Risk Levels
```python
# safety_categories.py
class SafetyCategories:
    def __init__(self):
        self.categories = {
            "hate_speech": {
                "risk_level": "HIGH",
                "detection_model": "gemini-2.5-flash",  # Cost-efficient
                "action": "block_and_flag"
            },
            "adult_content": {
                "risk_level": "HIGH", 
                "detection_model": "gemini-2.5-flash",
                "action": "block_and_regenerate"
            },
            "violence": {
                "risk_level": "HIGH",
                "detection_model": "gemini-2.5-flash",
                "action": "block_and_flag"
            },
            "misinformation": {
                "risk_level": "MEDIUM",
                "detection_model": "gemini-2.5-flash",
                "action": "warning_and_review"
            },
            "financial_advice": {
                "risk_level": "MEDIUM",
                "detection_model": "gemini-2.5-flash", 
                "action": "disclaimer_required"
            },
            "medical_advice": {
                "risk_level": "HIGH",
                "detection_model": "gemini-2.5-flash",
                "action": "block_and_redirect"
            }
        }
        
        # Brazilian-specific categories
        self.brazilian_categories = {
            "lgpd_violation": {
                "risk_level": "HIGH",
                "action": "block_and_audit"
            },
            "cultural_insensitivity": {
                "risk_level": "MEDIUM", 
                "action": "warning_and_educate"
            },
            "regional_stereotypes": {
                "risk_level": "MEDIUM",
                "action": "regenerate_with_guidance"
            }
        }
```

### 1.2 Multi-Layer Safety Architecture
```yaml
# safety_architecture.yaml
safety_layers:
  layer_1_preprocessing:
    model: "gemini-2.5-flash"
    cost_per_check: "~$0.0001"
    latency: "< 200ms"
    functions:
      - "Input sanitization"
      - "Basic content classification" 
      - "Brazilian context validation"
      
  layer_2_generation_monitoring:
    model: "gemini-2.5-flash" 
    real_time: true
    functions:
      - "Content generation monitoring"
      - "Real-time safety assessment"
      - "Brand alignment checking"
      
  layer_3_post_generation:
    model: "gemini-2.5-flash"
    cost_per_check: "~$0.0002"
    latency: "< 300ms"
    functions:
      - "Final content validation"
      - "Business appropriateness check"
      - "Legal compliance verification"
      
  layer_4_user_feedback:
    human_in_loop: true
    cost: "Manual review when needed"
    functions:
      - "User feedback processing"
      - "Continuous model improvement"
      - "Appeal handling"
```

## 2. Brand Safety Guidelines

### 2.1 Brazilian Business Brand Standards
```python
# brand_safety_brazilian.py
class BrazilianBrandSafety:
    def __init__(self):
        self.brand_guidelines = {
            "language_requirements": {
                "primary_language": "pt-BR",
                "tone": ["professional", "friendly", "accessible"],
                "avoid": ["overly_formal", "slang_heavy", "region_specific_jargon"]
            },
            "cultural_sensitivity": {
                "inclusive_language": True,
                "regional_awareness": True,
                "diversity_representation": True,
                "religious_neutrality": True
            },
            "business_appropriateness": {
                "industry_compliance": True,
                "professional_standards": True,
                "competitor_neutrality": True,
                "factual_accuracy": True
            }
        }
        
    def validate_brand_alignment(self, content, business_context):
        """Validate content against Brazilian brand safety standards"""
        score = 0
        issues = []
        
        # Language appropriateness (25%)
        if self.check_portuguese_quality(content):
            score += 25
        else:
            issues.append("Portuguese language quality concerns")
            
        # Cultural sensitivity (25%)
        if self.check_cultural_sensitivity(content):
            score += 25  
        else:
            issues.append("Cultural sensitivity issues detected")
            
        # Business appropriateness (25%)
        if self.check_business_context(content, business_context):
            score += 25
        else:
            issues.append("Business context mismatch")
            
        # Legal compliance (25%) 
        if self.check_legal_compliance(content):
            score += 25
        else:
            issues.append("Potential legal compliance issues")
            
        return {
            "score": score,
            "passed": score >= 75,
            "issues": issues,
            "recommendations": self.generate_recommendations(issues)
        }
```

### 2.2 Industry-Specific Safety Rules
```python
# industry_safety_rules.py
class IndustrySpecificSafety:
    def __init__(self):
        self.industry_rules = {
            "healthcare": {
                "restrictions": [
                    "No medical diagnosis claims",
                    "No treatment recommendations", 
                    "Require professional consultation disclaimers"
                ],
                "required_disclaimers": [
                    "Consulte sempre um profissional de saúde",
                    "Este conteúdo não substitui orientação médica"
                ]
            },
            "financial": {
                "restrictions": [
                    "No investment advice",
                    "No guaranteed return claims",
                    "No specific financial product endorsements"  
                ],
                "required_disclaimers": [
                    "Consulte um consultor financeiro qualificado",
                    "Investimentos envolvem riscos"
                ]
            },
            "food_service": {
                "restrictions": [
                    "No allergen-free guarantees without verification",
                    "No health claims without substantiation",
                    "Follow ANVISA guidelines"
                ],
                "required_disclaimers": [
                    "Verifique ingredientes para alergias",
                    "Informações nutricionais aproximadas"
                ]
            },
            "education": {
                "restrictions": [
                    "No false accreditation claims",
                    "No unrealistic outcome promises",
                    "Follow MEC guidelines"
                ],
                "required_disclaimers": [
                    "Resultados podem variar individualmente",
                    "Verifique reconhecimento oficial"
                ]
            }
        }
        
    def apply_industry_safety(self, content, industry):
        """Apply industry-specific safety rules"""
        if industry not in self.industry_rules:
            return {"approved": True, "modifications": []}
            
        rules = self.industry_rules[industry]
        modifications = []
        
        # Check for restricted content
        for restriction in rules["restrictions"]:
            if self.content_violates_restriction(content, restriction):
                modifications.append(f"Remove: {restriction}")
                
        # Add required disclaimers
        for disclaimer in rules["required_disclaimers"]:
            if disclaimer not in content:
                modifications.append(f"Add disclaimer: {disclaimer}")
                
        return {
            "approved": len(modifications) == 0,
            "modifications": modifications,
            "updated_content": self.apply_modifications(content, modifications)
        }
```

## 3. Content Moderation Pipeline

### 3.1 Real-Time Content Filtering
```python
# content_moderation_pipeline.py
class ContentModerationPipeline:
    def __init__(self):
        self.gemini_flash_client = GeminiFlashClient()  # Cost-efficient
        self.cost_per_moderation = 0.0001  # Very low cost
        
    async def moderate_content(self, content, business_context):
        """Main content moderation pipeline"""
        moderation_result = {
            "approved": False,
            "confidence_score": 0,
            "issues": [],
            "cost": 0,
            "processing_time": 0
        }
        
        start_time = time.time()
        
        try:
            # Step 1: Quick safety check (Gemini Flash)
            safety_check = await self.quick_safety_check(content)
            moderation_result["cost"] += self.cost_per_moderation
            
            if not safety_check["safe"]:
                moderation_result["issues"] = safety_check["issues"]
                return moderation_result
                
            # Step 2: Brand alignment check
            brand_check = await self.check_brand_alignment(content, business_context)
            moderation_result["cost"] += self.cost_per_moderation
            
            if not brand_check["aligned"]:
                moderation_result["issues"].extend(brand_check["issues"])
                return moderation_result
                
            # Step 3: Brazilian context validation
            brazilian_check = await self.validate_brazilian_context(content)
            moderation_result["cost"] += self.cost_per_moderation
            
            if brazilian_check["appropriate"]:
                moderation_result["approved"] = True
                moderation_result["confidence_score"] = brazilian_check["confidence"]
            else:
                moderation_result["issues"].extend(brazilian_check["issues"])
                
        except Exception as e:
            moderation_result["issues"].append(f"Moderation error: {str(e)}")
            
        finally:
            moderation_result["processing_time"] = time.time() - start_time
            
        return moderation_result
        
    async def quick_safety_check(self, content):
        """Fast safety check using Gemini Flash"""
        prompt = f"""
        Analise o seguinte conteúdo para problemas de segurança:
        
        Conteúdo: {content}
        
        Verifique:
        1. Discurso de ódio
        2. Conteúdo adulto inadequado
        3. Violência
        4. Informações médicas/financeiras inadequadas
        5. Desinformação
        
        Responda em JSON:
        {{"safe": true/false, "issues": ["lista", "de", "problemas"], "confidence": 0.0-1.0}}
        """
        
        response = await self.gemini_flash_client.generate(
            prompt=prompt,
            max_tokens=200,
            temperature=0.1
        )
        
        return json.loads(response.text)
```

### 3.2 Automated Content Enhancement
```python
# content_enhancement.py
class ContentEnhancer:
    def __init__(self):
        self.enhancement_cost = 0.0002  # Slightly higher for quality improvement
        
    async def enhance_content_safety(self, content, issues):
        """Automatically enhance content to address safety issues"""
        enhancement_prompt = f"""
        Melhore o seguinte conteúdo para resolver estes problemas:
        
        Conteúdo original: {content}
        Problemas identificados: {', '.join(issues)}
        
        Diretrizes para correção:
        1. Mantenha o tom profissional e amigável
        2. Use português brasileiro adequado
        3. Seja culturalmente sensível
        4. Mantenha relevância para pequenos negócios brasileiros
        5. Adicione disclaimers se necessário
        
        Retorne apenas o conteúdo corrigido.
        """
        
        response = await self.gemini_flash_client.generate(
            prompt=enhancement_prompt,
            max_tokens=500,
            temperature=0.3
        )
        
        return {
            "enhanced_content": response.text,
            "cost": self.enhancement_cost,
            "improvements_made": issues
        }
        
    async def add_appropriate_disclaimers(self, content, business_type):
        """Add appropriate disclaimers based on business type"""
        disclaimer_map = {
            "healthcare": "⚠️ Este conteúdo é informativo. Consulte sempre um profissional de saúde.",
            "financial": "⚠️ Consulte um especialista financeiro. Investimentos envolvem riscos.",
            "legal": "⚠️ Este conteúdo não constitui assessoria jurídica. Consulte um advogado.",
            "education": "⚠️ Resultados podem variar. Verifique credenciamentos oficiais.",
            "food": "⚠️ Verifique ingredientes para alergias. Informações aproximadas."
        }
        
        if business_type in disclaimer_map:
            disclaimer = disclaimer_map[business_type]
            enhanced_content = f"{content}\n\n{disclaimer}"
        else:
            enhanced_content = content
            
        return enhanced_content
```

## 4. Brazilian Context Validation

### 4.1 Cultural Appropriateness Checker
```python
# brazilian_context_validator.py
class BrazilianContextValidator:
    def __init__(self):
        self.cultural_markers = {
            "positive_markers": [
                "inclusivo", "diverso", "acessível", "familiar",
                "comunidade", "local", "brasileiro", "regional"
            ],
            "negative_markers": [
                "estereótipo", "preconceito", "discriminação",
                "exclusivo", "elitista", "inacessível"
            ],
            "regional_sensitivity": [
                "norte", "nordeste", "centro-oeste", "sudeste", "sul",
                "caipira", "carioca", "paulista", "gaúcho", "mineiro"
            ]
        }
        
    async def validate_cultural_appropriateness(self, content):
        """Validate cultural appropriateness for Brazilian market"""
        validation_prompt = f"""
        Analise este conteúdo para adequação cultural brasileira:
        
        Conteúdo: {content}
        
        Avalie:
        1. Linguagem inclusiva e respeitosa
        2. Sensibilidade regional (evitar estereótipos)
        3. Adequação para diversidade brasileira
        4. Tom adequado para micro-empreendedores
        5. Referências culturais apropriadas
        
        Responda em JSON:
        {{
            "appropriate": true/false,
            "confidence": 0.0-1.0,
            "issues": ["lista de problemas"],
            "suggestions": ["sugestões de melhoria"]
        }}
        """
        
        response = await self.gemini_flash_client.generate(
            prompt=validation_prompt,
            max_tokens=300,
            temperature=0.1
        )
        
        return json.loads(response.text)
        
    def check_regional_stereotypes(self, content):
        """Check for potentially harmful regional stereotypes"""
        stereotypes_found = []
        
        # Common Brazilian regional stereotypes to avoid
        problematic_patterns = [
            r"nordestino.*preguiçoso",
            r"paulista.*arrogante", 
            r"carioca.*malandro",
            r"gaúcho.*conservador",
            r"mineiro.*desconfiado"
        ]
        
        for pattern in problematic_patterns:
            if re.search(pattern, content.lower()):
                stereotypes_found.append(pattern)
                
        return {
            "has_stereotypes": len(stereotypes_found) > 0,
            "stereotypes": stereotypes_found,
            "severity": "high" if stereotypes_found else "none"
        }
```

### 4.2 Business Context Appropriateness
```python
# business_context_checker.py
class BusinessContextChecker:
    def __init__(self):
        self.business_standards = {
            "micro_entrepreneur": {
                "appropriate_tone": ["acessível", "prático", "encorajador"],
                "avoid_tone": ["técnico demais", "condescendente", "elitista"],
                "budget_awareness": True,
                "simplicity_focus": True
            }
        }
        
    async def validate_business_appropriateness(self, content, business_info):
        """Validate content appropriateness for business context"""
        validation_prompt = f"""
        Valide se este conteúdo é adequado para um micro-empreendedor brasileiro:
        
        Conteúdo: {content}
        Tipo de negócio: {business_info.get('type', 'geral')}
        Localização: {business_info.get('location', 'Brasil')}
        
        Critérios de validação:
        1. Linguagem acessível para micro-empreendedores
        2. Relevância para o tipo de negócio
        3. Consideração de orçamento limitado
        4. Praticidade das sugestões
        5. Adequação ao mercado local
        
        Responda em JSON:
        {{
            "appropriate": true/false,
            "relevance_score": 0.0-1.0,
            "accessibility_score": 0.0-1.0,
            "practicality_score": 0.0-1.0,
            "issues": ["problemas identificados"],
            "improvements": ["sugestões de melhoria"]
        }}
        """
        
        response = await self.gemini_flash_client.generate(
            prompt=validation_prompt,
            max_tokens=400,
            temperature=0.1
        )
        
        return json.loads(response.text)
```

## 5. Cost-Efficient Monitoring System

### 5.1 Smart Moderation Queue
```python
# smart_moderation_queue.py
class SmartModerationQueue:
    def __init__(self):
        self.queue_priorities = {
            "high_risk": {"priority": 1, "sla": "immediate"},
            "medium_risk": {"priority": 2, "sla": "< 5 minutes"},
            "low_risk": {"priority": 3, "sla": "< 30 minutes"},
            "routine_check": {"priority": 4, "sla": "< 2 hours"}
        }
        
        self.cost_optimization = {
            "batch_processing": True,
            "smart_routing": True,
            "cache_results": True,
            "adaptive_thoroughness": True
        }
        
    async def process_moderation_queue(self):
        """Process moderation queue with cost optimization"""
        batch_size = 10  # Process in cost-efficient batches
        
        while True:
            # Get high-priority items first
            items = await self.get_queued_items(batch_size)
            
            if not items:
                await asyncio.sleep(30)  # Wait for new items
                continue
                
            # Batch process for cost efficiency
            batch_results = await self.batch_moderate(items)
            
            # Update results and metrics
            await self.update_moderation_results(batch_results)
            
            # Cost tracking
            await self.track_moderation_costs(batch_results)
            
    async def batch_moderate(self, items):
        """Process multiple items in a single API call for cost efficiency"""
        batch_prompt = self.create_batch_moderation_prompt(items)
        
        response = await self.gemini_flash_client.generate(
            prompt=batch_prompt,
            max_tokens=1000,
            temperature=0.1
        )
        
        # Parse batch results
        results = self.parse_batch_results(response.text, items)
        
        return results
```

### 5.2 Performance Metrics & Cost Tracking
```python
# moderation_metrics.py
class ModerationMetrics:
    def __init__(self):
        self.metrics = {
            "total_content_moderated": 0,
            "content_blocked": 0,
            "content_enhanced": 0,
            "cost_per_moderation": 0,
            "average_processing_time": 0,
            "accuracy_score": 0
        }
        
    def track_moderation_performance(self):
        """Track moderation system performance and costs"""
        daily_stats = {
            "date": datetime.now().date(),
            "total_moderations": self.metrics["total_content_moderated"],
            "block_rate": self.metrics["content_blocked"] / max(1, self.metrics["total_content_moderated"]),
            "enhancement_rate": self.metrics["content_enhanced"] / max(1, self.metrics["total_content_moderated"]),
            "daily_cost": self.calculate_daily_moderation_cost(),
            "cost_efficiency": self.calculate_cost_efficiency()
        }
        
        return daily_stats
        
    def calculate_daily_moderation_cost(self):
        """Calculate daily moderation costs"""
        base_cost_per_check = 0.0001  # Gemini Flash cost
        enhancement_cost_per_check = 0.0002
        
        total_cost = (
            (self.metrics["total_content_moderated"] * base_cost_per_check) +
            (self.metrics["content_enhanced"] * enhancement_cost_per_check)
        )
        
        return total_cost
        
    def optimize_moderation_costs(self):
        """Suggest cost optimizations"""
        suggestions = []
        
        if self.metrics["content_blocked"] / self.metrics["total_content_moderated"] > 0.1:
            suggestions.append("High block rate - consider improving prompt quality")
            
        if self.calculate_daily_moderation_cost() > 10.0:  # R$ 10/day threshold
            suggestions.append("Consider implementing more aggressive caching")
            
        if self.metrics["average_processing_time"] > 1000:  # 1 second
            suggestions.append("Implement batch processing for better efficiency")
            
        return suggestions
```

## 6. User Feedback & Appeals System

### 6.1 Content Appeals Process
```python
# appeals_system.py
class ContentAppealsSystem:
    def __init__(self):
        self.appeal_categories = {
            "false_positive": "Content was incorrectly flagged",
            "cultural_context": "Missing Brazilian cultural context",
            "business_relevance": "Content is relevant to business",
            "technical_error": "System error in moderation"
        }
        
    async def process_appeal(self, appeal_data):
        """Process user appeal for moderated content"""
        appeal_prompt = f"""
        Reavalie este conteúdo baseado no apelo do usuário:
        
        Conteúdo original: {appeal_data['original_content']}
        Motivo da moderação: {appeal_data['moderation_reason']}
        Apelo do usuário: {appeal_data['appeal_reason']}
        Categoria: {appeal_data['category']}
        
        Critérios para reavaliação:
        1. Contexto cultural brasileiro
        2. Relevância para micro-empreendedores
        3. Adequação do tom e linguagem
        4. Possível interpretação incorreta inicial
        
        Responda em JSON:
        {{
            "appeal_approved": true/false,
            "confidence": 0.0-1.0,
            "explanation": "explicação da decisão",
            "revised_content": "conteúdo revisado se necessário"
        }}
        """
        
        response = await self.gemini_flash_client.generate(
            prompt=appeal_prompt,
            max_tokens=500,
            temperature=0.2
        )
        
        return json.loads(response.text)
```

### 6.2 Continuous Learning System
```python
# continuous_learning.py
class ContinuousLearningSystem:
    def __init__(self):
        self.learning_data = {
            "successful_appeals": [],
            "user_feedback": [],
            "context_improvements": [],
            "false_positives": []
        }
        
    async def learn_from_feedback(self, feedback_data):
        """Learn from user feedback to improve moderation"""
        # Analyze patterns in successful appeals
        if feedback_data["type"] == "successful_appeal":
            pattern = self.extract_pattern(feedback_data)
            self.learning_data["successful_appeals"].append(pattern)
            
        # Update moderation prompts based on learning
        if len(self.learning_data["successful_appeals"]) > 10:
            await self.update_moderation_prompts()
            
    def extract_pattern(self, feedback_data):
        """Extract learning patterns from feedback"""
        return {
            "content_type": feedback_data["content_type"],
            "business_type": feedback_data["business_type"],
            "issue_category": feedback_data["issue_category"],
            "resolution": feedback_data["resolution"],
            "cultural_context": feedback_data.get("cultural_context", ""),
            "timestamp": datetime.now().isoformat()
        }
        
    async def update_moderation_prompts(self):
        """Update moderation prompts based on learning"""
        # Analyze common false positives
        common_issues = self.analyze_common_issues()
        
        # Generate improved prompts
        for issue in common_issues:
            improved_prompt = await self.generate_improved_prompt(issue)
            self.update_prompt_library(issue["category"], improved_prompt)
```

## 7. Compliance & Legal Framework

### 7.1 LGPD Compliance in Content Moderation
```python
# lgpd_compliance.py
class LGPDComplianceChecker:
    def __init__(self):
        self.lgpd_requirements = {
            "data_minimization": True,
            "purpose_limitation": True,
            "transparency": True,
            "user_consent": True,
            "data_protection": True
        }
        
    def check_lgpd_compliance(self, content, personal_data_context):
        """Check content for LGPD compliance issues"""
        compliance_issues = []
        
        # Check for personal data exposure
        if self.contains_personal_data(content):
            compliance_issues.append("Contains personal data without proper handling")
            
        # Check for data collection mentions
        if self.mentions_data_collection(content):
            compliance_issues.append("Mentions data collection without consent notice")
            
        # Check for marketing consent
        if self.requires_marketing_consent(content):
            compliance_issues.append("Marketing content without proper consent mechanism")
            
        return {
            "compliant": len(compliance_issues) == 0,
            "issues": compliance_issues,
            "recommendations": self.generate_lgpd_recommendations(compliance_issues)
        }
```

### 7.2 Industry Regulation Compliance
```yaml
# industry_regulations.yaml
brazilian_regulations:
  anvisa:  # Health/Food
    scope: ["healthcare", "food", "cosmetics", "supplements"]
    requirements:
      - "No unauthorized health claims"
      - "Proper ingredient disclosure"
      - "Warning labels where required"
    
  cvm:  # Financial
    scope: ["financial", "investment", "insurance"]
    requirements:
      - "No investment advice without authorization"
      - "Risk disclosure required"
      - "Clear disclaimer language"
      
  mec:  # Education  
    scope: ["education", "courses", "training"]
    requirements:
      - "No false accreditation claims"
      - "Proper certification disclosure"
      - "Realistic outcome expectations"
      
  procon:  # Consumer Protection
    scope: ["all_businesses"]
    requirements:
      - "Clear pricing information"
      - "No misleading advertising"
      - "Proper return/refund policies"
```

## 8. Implementation & Deployment

### 8.1 Safety System Rollout Plan
```yaml
# rollout_plan.yaml
phase_1:  # Weeks 1-2
  - "Implement basic safety checks with Gemini Flash"
  - "Deploy Brazilian context validation"
  - "Setup cost monitoring"
  
phase_2:  # Weeks 3-4
  - "Add industry-specific rules"
  - "Implement appeals system"
  - "Deploy batch processing optimization"
  
phase_3:  # Weeks 5-6
  - "Add continuous learning system"
  - "Implement LGPD compliance checking"
  - "Deploy advanced enhancement features"
  
phase_4:  # Weeks 7-8
  - "Full monitoring and alerting"
  - "Performance optimization"
  - "Documentation and training"
```

### 8.2 Cost Management Strategy
```python
# cost_management.py
class SafetyCostManager:
    def __init__(self):
        self.cost_targets = {
            "daily_moderation_budget": 20.0,  # R$ 20/day
            "cost_per_moderation": 0.0003,    # R$ 0.0003 per check
            "monthly_safety_budget": 600.0    # R$ 600/month
        }
        
    def optimize_safety_costs(self):
        """Optimize safety system costs"""
        optimizations = {
            "batch_processing": "Process 10+ items per API call",
            "smart_caching": "Cache results for 24 hours",
            "risk_based_routing": "Use cheaper models for low-risk content",
            "automated_enhancement": "Auto-fix instead of manual review"
        }
        
        projected_savings = {
            "batch_processing": "60% cost reduction",
            "smart_caching": "40% cost reduction", 
            "risk_based_routing": "30% cost reduction",
            "automated_enhancement": "50% manual review cost reduction"
        }
        
        return {
            "optimizations": optimizations,
            "projected_savings": projected_savings,
            "implementation_priority": [
                "batch_processing",
                "smart_caching", 
                "automated_enhancement",
                "risk_based_routing"
            ]
        }
```

This comprehensive safety and brand guardrails framework ensures the AI Departments Platform generates appropriate, safe, and culturally sensitive content for Brazilian micro-entrepreneurs while maintaining cost efficiency through smart use of Gemini 2.5 Flash for most moderation tasks.