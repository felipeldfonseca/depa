# Business Template Library
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Felipe PM + Claude  
**Status:** APPROVED  

---

## Template Library Overview

### Purpose
The Template Library provides pre-configured business setups that enable micro-entrepreneurs to launch their AI departments with industry-specific content, workflows, and configurations. Templates eliminate the complexity of starting from scratch and provide proven frameworks for success.

### Template Strategy
- **Industry-Specific**: Tailored to common Brazilian micro-entrepreneur verticals
- **MVP Focus**: 3 core templates for Phase 1 launch
- **Expansion Ready**: Framework for adding 10+ templates in Phase 2
- **Cultural Relevance**: Brazilian business practices and customer expectations
- **Performance Optimized**: Based on successful micro-entrepreneur patterns

---

## Template Architecture

### Template Components
```yaml
template_structure:
  metadata:
    id: string                    # unique-identifier
    name: string                  # "Fashion & Apparel"
    description: string           # Business description
    target_audience: string[]     # ["B2C", "young-adults"]
    business_model: string        # "retail", "service", "digital"
    difficulty_level: string      # "beginner", "intermediate", "advanced"
    
  departments:
    marketing:
      agents: {}                  # Pre-configured agents
      content_calendar: {}        # 30-day starter calendar
      brand_guidelines: {}        # Tone, style, messaging
      
    customer_service:
      faq_base: {}               # Common questions & answers
      response_templates: {}      # Message templates
      escalation_rules: {}       # When to involve human
      
  business_configuration:
    operating_hours: string      # "9h às 18h"
    timezone: string             # "America/Sao_Paulo"
    payment_methods: string[]    # ["PIX", "Cartão", "Boleto"]
    delivery_options: string[]   # ["Correios", "Entrega local"]
    
  sample_content:
    social_posts: []             # Example posts
    whatsapp_responses: []       # Example responses
    brand_assets: []             # Logo examples, color palettes
```

### Template Inheritance
```python
# Template inheritance system
class BaseTemplate:
    def __init__(self):
        self.base_components = {
            'customer_service': {
                'greeting': "Oi! Como posso te ajudar hoje? 😊",
                'business_hours_response': "Nosso horário é {hours}. Te respondo assim que possível!",
                'escalation_trigger': "Vou te conectar com nossa equipe humana para te ajudar melhor."
            },
            'marketing': {
                'posting_schedule': "Segunda, Quarta, Sexta às 10h",
                'hashtag_strategy': "3-5 hashtags relevantes + 2-3 de nicho",
                'content_mix': "70% educativo, 20% promocional, 10% pessoal"
            }
        }
    
    def customize_for_industry(self, industry: str, customizations: dict):
        # Apply industry-specific customizations to base template
        pass

class FashionTemplate(BaseTemplate):
    def __init__(self):
        super().__init__()
        self.industry_customizations = {
            'marketing': {
                'content_focus': ['looks_do_dia', 'dicas_de_estilo', 'tendencias'],
                'seasonal_campaigns': ['verao', 'inverno', 'black_friday'],
                'user_generated_content': True
            },
            'customer_service': {
                'size_guide_integration': True,
                'return_policy_prominence': True,
                'styling_advice': True
            }
        }
```

---

## Phase 1 Templates (MVP)

### 1. Fashion & Apparel Template

#### Target Market
- **Business Type**: Clothing, accessories, footwear retailers
- **Typical Size**: Solo entrepreneur to 3 employees
- **Revenue Range**: R$10K - R$100K monthly
- **Primary Channels**: Instagram, WhatsApp, physical showroom
- **Key Challenges**: Visual content creation, size guidance, inventory management

#### Department Configurations

##### Marketing Department
```yaml
marketing_config:
  agents:
    social_media:
      content_types:
        - look_do_dia: "Daily outfit inspiration posts"
        - product_showcase: "Individual product highlights"
        - styling_tips: "How to wear/combine pieces"
        - customer_features: "Customer photos wearing products"
        - behind_scenes: "Production, sourcing, designer process"
        
      posting_schedule:
        instagram_feed: "Segunda, Quarta, Sexta às 10h"
        instagram_stories: "Diário às 9h e 15h"
        facebook: "Terça e quinta às 14h"
        
      hashtag_strategy:
        branded: ["#{business_name}", "#{business_city}"]
        niche: ["#modafeminina", "#lookdodia", "#estiloBrasil"]
        trending: ["#tendencia2025", "#modaconsciente"]
        
    content_calendar:
      weekly_themes:
        monday: "Motivation Monday - Looks inspiradores"
        tuesday: "Tip Tuesday - Dicas de styling"
        wednesday: "What's New Wednesday - Novidades"
        thursday: "Throwback Thursday - Peças clássicas"
        friday: "Fashion Friday - Tendências"
        saturday: "Social Saturday - Clientes usando nossas peças"
        sunday: "Sunday Style - Looks casuais"
        
      seasonal_campaigns:
        - name: "Coleção Verão"
          duration: "Dezembro - Março"
          focus: "Peças leves, cores vibrantes, praia"
        - name: "Volta às Aulas"
          duration: "Janeiro - Fevereiro"
          focus: "Looks casuais e profissionais"
        - name: "Dia das Mães"
          duration: "Abril - Maio"
          focus: "Presentes especiais, lookbooks mãe e filha"
        - name: "Festa Junina"
          duration: "Junho"
          focus: "Looks temáticos, estampas tradicionais"
        - name: "Black Friday"
          duration: "Novembro"
          focus: "Promoções, liquidação, combos"
          
  brand_guidelines:
    tone_of_voice: "Amigável, empoderador, inclusivo"
    visual_style: "Clean, natural lighting, diverse models"
    color_palette: ["#FF6B9D", "#FFEAA7", "#74B9FF"]
    photography_style: "Natural lighting, real customers, lifestyle shots"
    
  content_prompts:
    product_post: |
      Crie um post Instagram para {product_name}. 
      Destaque: estilo versátil, qualidade, como combinar.
      Tom: empoderador e acessível.
      Inclua call-to-action para DM ou WhatsApp.
      Máximo: 150 caracteres.
      
    styling_tip: |
      Crie uma dica de como usar {item_category}.
      Foco: praticidade, múltiplas ocasiões.
      Tom: amigável e didático.
      Formato: dica rápida com exemplo.
```

##### Customer Service Department
```yaml
customer_service_config:
  faq_base:
    sizing:
      - question: "Como escolher o tamanho certo?"
        answer: "Temos tabela de medidas completa! Me passa sua medida do busto/cintura/quadril que te ajudo a escolher o tamanho perfeito 📏"
        follow_up: "size_guide_link"
        
      - question: "As peças vestem certinho?"
        answer: "Nossas peças seguem numeração brasileira padrão. Se tiver dúvida entre dois tamanhos, recomendo o maior para mais conforto! 😊"
        
    orders:
      - question: "Como faço meu pedido?"
        answer: "Super fácil! Me fala qual peça te interessou que eu monto seu orçamento personalizado. Aceitamos PIX, cartão e parcelamos em até 6x! 💳"
        
      - question: "Qual o prazo de entrega?"
        answer: "Para {city}: 2-3 dias úteis. Para outras cidades: 5-7 dias úteis. Te passo o código de rastreamento assim que postar! 📦"
        
    returns:
      - question: "Posso trocar se não servir?"
        answer: "Claro! Você tem 7 dias para trocar por outro tamanho ou cor. A peça só precisa estar com etiqueta e sem uso 🔄"
        
      - question: "Como faço a troca?"
        answer: "Me manda foto da peça e diz qual tamanho/cor quer. Eu organizo a troca - você só paga o frete! Super simples 😉"
        
  response_templates:
    greeting: "Oi lindeza! 👋 Seja bem-vinda à {business_name}! Como posso te ajudar hoje? 😊"
    
    product_inquiry: |
      Ótima escolha! {product_name} é um dos nossos queridinhos! 💕
      
      Temos nas cores: {available_colors}
      Tamanhos: {available_sizes}
      Valor: R$ {price} (parcelamos em até 6x)
      
      Qual tamanho e cor você gostaria? 
      
    out_of_stock: |
      Ai que pena! {product_name} está em falta no tamanho {size} 😔
      
      Mas tenho boas notícias:
      ✨ Chega reposição na próxima semana
      ✨ Posso te avisar quando chegar
      ✨ Temos peças similares disponíveis
      
      O que prefere?
      
    closing: "Qualquer dúvida, é só chamar! Estamos aqui para te ajudar sempre! 🥰✨"
    
  escalation_rules:
    triggers:
      - complex_return_request
      - payment_dispute
      - product_defect_complaint
      - custom_order_request
      - bulk_order_inquiry
      
    escalation_message: "Vou te conectar com nossa estilista para te dar o melhor atendimento! Só um minutinho! 💕"
```

#### Sample Content Pack
```yaml
sample_content:
  social_posts:
    - type: "product_showcase"
      text: "Essa blusa é AMOR! 💕 Versátil, confortável e com um caimento que valoriza todas! Quem mais é apaixonada por peças coringas? ✨"
      hashtags: ["#blusafeminina", "#lookdodia", "#modaversatil"]
      
    - type: "styling_tip"
      text: "DICA: Blusa social + saia midi = look perfeito para o trabalho! E depois é só trocar por uma calça jeans e virar look casual! 👗→👖"
      hashtags: ["#dicasdestilo", "#looktrabalho", "#modapratica"]
      
    - type: "customer_feature"
      text: "A @cliente arrasando com nossa saia plissada! 😍 Assim que gosto de ver: vocês lindas e confiantes! Marca uma amiga que precisa dessa saia!"
      hashtags: ["#clientearrasando", "#saiaprissada", "#autoestima"]
      
  whatsapp_responses:
    - scenario: "customer_asking_about_dress"
      response: "Oi! Esse vestido é um arraso! Super elegante e confortável. Temos em preto, azul marinho e vinho. Qual cor chama mais sua atenção? 😊"
      
    - scenario: "size_doubt"
      response: "Te entendo perfeitamente! Para escolher certinho, me passa suas medidas (busto, cintura, quadril) que eu te falo exatamente qual tamanho! 📏"
      
  brand_assets:
    color_palette:
      primary: "#D63384"    # Pink elegante
      secondary: "#6F42C1"  # Roxo sofisticado
      accent: "#FFC107"     # Dourado
      neutral: "#6C757D"    # Cinza moderno
      
    logo_guidelines:
      style: "Script elegante com serif moderna"
      applications: "Etiquetas, posts, assinatura WhatsApp"
      
    photography_style:
      lighting: "Natural, golden hour preferível"
      backgrounds: "Neutros, texturas naturais"
      models: "Diversas, expressões naturais e confiantes"
```

### 2. Food & Beverage Template

#### Target Market
- **Business Type**: Restaurants, cafés, food delivery, catering
- **Typical Size**: Family business, 2-8 employees
- **Revenue Range**: R$20K - R$200K monthly
- **Primary Channels**: WhatsApp, Instagram, delivery apps
- **Key Challenges**: Food photography, order management, peak hour handling

#### Department Configurations

##### Marketing Department
```yaml
marketing_config:
  agents:
    social_media:
      content_types:
        - prato_do_dia: "Daily special showcase"
        - processo_cozinha: "Behind-the-scenes cooking"
        - ingredientes_frescos: "Fresh ingredient highlights"
        - clientes_satisfeitos: "Happy customers enjoying food"
        - dicas_culinarias: "Cooking tips and tricks"
        
      posting_schedule:
        instagram_feed: "Segunda, Quarta, Sexta às 11h"
        instagram_stories: "Diário às 8h (café), 12h (almoço), 19h (jantar)"
        facebook: "Terça e quinta às 10h"
        
      hashtag_strategy:
        local: ["#{business_city}gastro", "#{neighborhood}food"]
        cuisine: ["#comidacaseira", "#sabortradicionai", "#cozinhabrasileira"]
        trending: ["#delivery{city}", "#gastronomia", "#pratofeito"]
        
    content_calendar:
      daily_themes:
        monday: "Menu Monday - Cardápio da semana"
        tuesday: "Tradition Tuesday - Pratos tradicionais"
        wednesday: "What's Cooking Wednesday - Processo de preparo"
        thursday: "Throwback Thursday - Receitas da família"
        friday: "Fresh Friday - Ingredientes especiais"
        saturday: "Social Saturday - Clientes aproveitando"
        sunday: "Sunday Special - Prato especial domingo"
        
      seasonal_campaigns:
        - name: "Festa Junina"
          duration: "Junho"
          focus: "Pratos típicos, quentão, doces juninos"
        - name: "Dezembro/Festividades"
          duration: "Dezembro"
          focus: "Ceia natalina, encomendas especiais"
        - name: "Verão Refrescante"
          duration: "Janeiro - Março"
          focus: "Pratos leves, saladas, sucos naturais"
          
  brand_guidelines:
    tone_of_voice: "Caloroso, familiar, acolhedor"
    visual_style: "Cores quentes, comida apetitosa, ambiente aconchegante"
    color_palette: ["#E74C3C", "#F39C12", "#27AE60"]
    photography_style: "Natural lighting, close-ups de comida, ambiente familiar"
    
  content_prompts:
    prato_destaque: |
      Crie um post para {dish_name}.
      Destaque: ingredientes frescos, preparo artesanal, sabor caseiro.
      Tom: acolhedor e apetitoso.
      Inclua call-to-action para pedidos.
      Máximo: 140 caracteres.
      
    behind_scenes: |
      Mostre o preparo de {dish_name}.
      Foco: carinho no preparo, qualidade dos ingredientes.
      Tom: familiar e autêntico.
      Formato: processo passo-a-passo.
```

##### Customer Service Department
```yaml
customer_service_config:
  faq_base:
    menu:
      - question: "Qual o cardápio de hoje?"
        answer: "Hoje temos: {daily_menu}. Todos os pratos vêm com acompanhamentos. Quer saber mais sobre algum? 🍽️"
        
      - question: "Têm opções vegetarianas?"
        answer: "Sim! Temos várias opções vegetarianas deliciosas: {veggie_options}. Todas feitas com muito carinho! 🌱"
        
    delivery:
      - question: "Fazem entrega?"
        answer: "Sim! Entregamos em {delivery_areas}. Taxa: R${delivery_fee}. Pedido mínimo: R${minimum_order}. Tempo: {delivery_time} 🚚"
        
      - question: "Quanto tempo demora?"
        answer: "Preparo: {prep_time} + Entrega: {delivery_time} = Total: {total_time}. Te avisamos quando sair! ⏰"
        
    orders:
      - question: "Como fazer pedido?"
        answer: "É só me falar o que quer! Confirmo o valor, endereço e forma de pagamento. Aceitamos PIX, dinheiro e cartão na entrega! 💳"
        
      - question: "Posso pagar na entrega?"
        answer: "Claro! Aceitamos dinheiro e cartão na entrega. Se for PIX, tem 5% de desconto! 💰"
        
  response_templates:
    greeting: "Olá! Bem-vindo(a) ao {business_name}! 🍽️ Estou aqui para te ajudar com seu pedido! O que vai querer hoje?"
    
    order_taking: |
      Perfeito! Anotei aqui:
      
      🍽️ {order_items}
      💰 Total: R$ {total_price}
      📍 Endereço: {delivery_address}
      
      Confirma pra mim? Tempo total: {estimated_time}
      
    order_confirmation: |
      Pedido confirmado! 🎉
      
      📝 Número: #{order_number}
      ⏰ Previsão: {estimated_time}
      💰 Valor: R$ {total}
      
      Já começamos a preparar com carinho! Te aviso quando sair para entrega 😊
      
    out_of_stock: |
      Opa! Infelizmente o {item_name} acabou agora pouco 😔
      
      Mas posso te sugerir:
      🍽️ {alternative_1} - Similar e delicioso!
      🍽️ {alternative_2} - Sucesso entre os clientes!
      
      Qual te interessa mais?
      
  escalation_rules:
    triggers:
      - custom_order_large_quantity
      - complaint_about_food_quality
      - delivery_delay_complaint
      - special_dietary_requirements
      - catering_inquiry
      
    escalation_message: "Vou te conectar com o chef/proprietário para te atender pessoalmente! Um momentinho! 👨‍🍳"
```

### 3. Digital Services Template

#### Target Market
- **Business Type**: Consultants, coaches, digital marketers, developers
- **Typical Size**: Solo entrepreneur or small team
- **Revenue Range**: R$15K - R$80K monthly
- **Primary Channels**: LinkedIn, Instagram, WhatsApp, website
- **Key Challenges**: Trust building, service explanation, lead nurturing

#### Department Configurations

##### Marketing Department
```yaml
marketing_config:
  agents:
    social_media:
      content_types:
        - dicas_gratuitas: "Free tips and insights"
        - case_studies: "Client success stories"
        - processo_trabalho: "How I work with clients"
        - industria_insights: "Industry trends and analysis"
        - personal_branding: "Professional development content"
        
      posting_schedule:
        linkedin: "Segunda, Quarta, Sexta às 9h"
        instagram_feed: "Terça e quinta às 15h"
        instagram_stories: "Diário às 8h e 17h"
        
      hashtag_strategy:
        professional: ["#consultoria", "#marketing", "#empreendedorismo"]
        personal: ["#{service_type}brasil", "#{expertise_area}"]
        educational: ["#dicasgratis", "#conhecimento", "#aprendizado"]
        
    content_calendar:
      weekly_themes:
        monday: "Motivation Monday - Inspiração profissional"
        tuesday: "Tip Tuesday - Dicas práticas gratuitas"
        wednesday: "Work Wednesday - Processo e metodologia"
        thursday: "Think Thursday - Reflexões sobre o mercado"
        friday: "Feature Friday - Case de sucesso"
        saturday: "Social Saturday - Bastidores pessoais"
        sunday: "Sunday Study - Aprendizado contínuo"
        
  brand_guidelines:
    tone_of_voice: "Profissional, educativo, confiável"
    visual_style: "Clean, profissional, cores corporativas"
    color_palette: ["#2C3E50", "#3498DB", "#E67E22"]
    photography_style: "Profissional, ambiente de trabalho, gráficos limpos"
```

##### Customer Service Department
```yaml
customer_service_config:
  faq_base:
    services:
      - question: "Que tipo de consultoria você faz?"
        answer: "Trabalho com {service_description}. Meu foco é {main_focus}. Quer saber como posso ajudar seu negócio especificamente? 🚀"
        
      - question: "Como funciona o processo?"
        answer: "É bem simples: 1) Diagnóstico gratuito 2) Proposta personalizada 3) Execução com acompanhamento. Quer agendar uma conversa? 📅"
        
    pricing:
      - question: "Quanto custa?"
        answer: "O investimento varia conforme suas necessidades. Após o diagnóstico gratuito, apresento uma proposta personalizada. Que tal conversarmos? 💼"
        
      - question: "Têm planos mensais?"
        answer: "Sim! Trabalho tanto com projetos pontuais quanto acompanhamento mensal. Vamos entender qual formato faz mais sentido para você? 📈"
        
  response_templates:
    greeting: "Olá! 👋 Obrigado(a) pelo interesse em meus serviços! Sou {professional_name}, {title}. Como posso te ajudar hoje?"
    
    service_inquiry: |
      Ótimo! {service_name} é uma das minhas especialidades! 🎯
      
      Para te dar a melhor orientação:
      📋 Me conta um pouco sobre seu negócio
      🎯 Qual seu principal desafio hoje
      📈 Que resultado quer alcançar
      
      Assim consigo te ajudar de forma mais assertiva!
      
    consultation_offer: |
      Que tal agendarmos uma conversa de 30 minutos para:
      
      ✅ Entender sua situação atual
      ✅ Identificar oportunidades
      ✅ Apresentar como posso ajudar
      
      É gratuito e sem compromisso! Quando você tem disponibilidade? 📅
```

---

## Template Customization System

### Dynamic Customization Engine
```python
class TemplateCustomizer:
    def __init__(self, base_template: str):
        self.base_template = self.load_template(base_template)
        self.customizations = {}
        
    def customize_for_business(
        self, 
        business_info: dict, 
        preferences: dict
    ) -> CustomizedTemplate:
        # Business-specific customizations
        customized = self.base_template.copy()
        
        # Apply business name throughout
        customized = self.replace_placeholders(customized, {
            'business_name': business_info.get('name'),
            'business_city': business_info.get('city'),
            'owner_name': business_info.get('owner_name')
        })
        
        # Apply industry-specific adjustments
        if business_info.get('sub_niche'):
            customized = self.apply_niche_customizations(
                customized, 
                business_info['sub_niche']
            )
            
        # Apply preference-based adjustments
        customized = self.apply_preference_customizations(
            customized,
            preferences
        )
        
        return CustomizedTemplate(customized)
    
    def generate_content_variations(
        self, 
        template: CustomizedTemplate,
        count: int = 30
    ) -> List[ContentPiece]:
        # Generate month's worth of content variations
        content_pieces = []
        
        for i in range(count):
            piece = self.generate_content_piece(
                template=template,
                variation_seed=i,
                ensure_diversity=True
            )
            content_pieces.append(piece)
            
        return content_pieces
```

### Template Marketplace Framework
```yaml
# Framework for future template marketplace
template_marketplace:
  structure:
    official_templates:
      created_by: "AI Departments Team"
      quality_assurance: "Full QA process"
      support_level: "Complete support"
      pricing: "Included in subscription"
      
    verified_templates:
      created_by: "Verified partners"
      quality_assurance: "Partner QA + our review"
      support_level: "Partner support + our escalation"
      pricing: "Revenue share model"
      
    community_templates:
      created_by: "Community members"
      quality_assurance: "Community review + automated checks"
      support_level: "Community support"
      pricing: "Free or community-set pricing"
      
  submission_process:
    1. template_creation:
       - Use template builder interface
       - Include all required components
       - Add sample content and configurations
       
    2. quality_review:
       - Automated technical validation
       - Content quality assessment
       - Cultural appropriateness check
       
    3. community_testing:
       - Beta testing with volunteer businesses
       - Feedback collection and iteration
       - Performance metrics gathering
       
    4. approval_publishing:
       - Final approval from our team
       - Marketplace publication
       - Revenue sharing setup
```

---

## Template Performance Metrics

### Success Tracking
```python
class TemplateAnalytics:
    def track_template_performance(self, template_id: str) -> TemplateMetrics:
        return TemplateMetrics(
            adoption_rate=self.calculate_adoption_rate(template_id),
            completion_rate=self.calculate_completion_rate(template_id),
            success_indicators={
                'first_week_activity': self.get_first_week_metrics(template_id),
                'content_generation_success': self.get_content_metrics(template_id),
                'customer_satisfaction': self.get_satisfaction_metrics(template_id),
                'business_growth_correlation': self.get_growth_metrics(template_id)
            },
            user_feedback=self.get_user_feedback(template_id),
            iteration_opportunities=self.identify_improvements(template_id)
        )
    
    def compare_template_performance(self) -> TemplateComparison:
        # Compare all templates to identify best practices
        return {
            'top_performing': self.get_top_templates(),
            'improvement_needed': self.get_underperforming_templates(),
            'user_preferences': self.analyze_user_preferences(),
            'content_effectiveness': self.analyze_content_performance()
        }
```

### Template Optimization
```yaml
optimization_framework:
  data_collection:
    user_interactions:
      - template_selection_reasons
      - customization_choices
      - content_approval_rates
      - feature_usage_patterns
      
    business_outcomes:
      - time_to_first_post
      - content_engagement_rates
      - customer_response_rates
      - revenue_correlation
      
  improvement_process:
    monthly_review:
      - Analyze performance metrics
      - Identify underperforming elements
      - Gather user feedback
      - Plan improvements
      
    quarterly_updates:
      - Release template improvements
      - Add new content variations
      - Update industry best practices
      - Introduce seasonal adaptations
      
    annual_overhaul:
      - Complete template review
      - Market trend integration
      - User journey optimization
      - Technology improvements
```

---

## Implementation Roadmap

### Phase 1: MVP Templates (Q1 2025)
- ✅ Fashion & Apparel template
- ✅ Food & Beverage template  
- ✅ Digital Services template
- Template customization engine
- Basic performance tracking

### Phase 2: Template Expansion (Q2 2025)
- Fitness & Wellness template
- Beauty & Aesthetics template
- Home Services template
- Education & Courses template
- Template marketplace framework

### Phase 3: Advanced Features (Q3 2025)
- AI-powered template recommendations
- Advanced customization options
- Community template submissions
- Template performance optimization
- Multi-language template support

### Phase 4: Marketplace Launch (Q4 2025)
- Public template marketplace
- Partner creator program
- Revenue sharing system
- Template creation tools
- Community features

---

**Document Status:** Template library specification complete with 3 MVP templates ready for implementation. Framework established for future expansion and marketplace development.