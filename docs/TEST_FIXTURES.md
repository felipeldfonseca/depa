# Test Fixtures & Mock Data
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Felipe PM + Claude  
**Status:** DEVELOPMENT READY  

---

## Overview

Comprehensive test fixtures and mock data for development, testing, and demonstration of the AI Departments Platform. These fixtures enable realistic testing scenarios while maintaining data privacy and LGPD compliance.

**Design Principles:**
- Realistic Brazilian business data
- Anonymized but representative
- Scalable test scenarios
- Performance testing ready
- Cross-department integration testing

---

## 1. User & Organization Test Data

### 1.1 Test Users
```json
// test_users.json
{
  "test_users": [
    {
      "id": "user_001",
      "email": "maria.silva@example.com",
      "name": "Maria Silva Santos",
      "role": "owner",
      "phone": "+5511999887766",
      "created_at": "2024-01-15T10:00:00Z",
      "last_login": "2024-12-13T09:15:00Z",
      "preferences": {
        "language": "pt-BR",
        "timezone": "America/Sao_Paulo",
        "notifications": {
          "whatsapp": true,
          "email": true,
          "push": false
        }
      },
      "onboarding": {
        "completed": true,
        "steps_completed": ["profile", "business", "integrations", "first_campaign"],
        "completion_date": "2024-01-16T14:30:00Z"
      }
    },
    {
      "id": "user_002", 
      "email": "carlos.mendes@example.com",
      "name": "Carlos Eduardo Mendes",
      "role": "owner",
      "phone": "+5521987654321",
      "created_at": "2024-02-20T16:45:00Z",
      "last_login": "2024-12-12T18:22:00Z",
      "preferences": {
        "language": "pt-BR",
        "timezone": "America/Sao_Paulo",
        "notifications": {
          "whatsapp": true,
          "email": false,
          "push": true
        }
      },
      "onboarding": {
        "completed": false,
        "steps_completed": ["profile", "business"],
        "current_step": "integrations"
      }
    },
    {
      "id": "user_003",
      "email": "fernanda.costa@example.com", 
      "name": "Fernanda Costa Lima",
      "role": "manager",
      "phone": "+5531912345678",
      "created_at": "2024-03-10T11:20:00Z",
      "last_login": "2024-12-13T07:45:00Z",
      "preferences": {
        "language": "pt-BR",
        "timezone": "America/Sao_Paulo",
        "notifications": {
          "whatsapp": true,
          "email": true,
          "push": true
        }
      },
      "onboarding": {
        "completed": true,
        "steps_completed": ["profile", "business", "integrations", "first_campaign", "team_setup"],
        "completion_date": "2024-03-12T16:15:00Z"
      }
    }
  ]
}
```

### 1.2 Test Organizations
```json
// test_organizations.json
{
  "test_organizations": [
    {
      "id": "org_001",
      "name": "Boutique Fernanda Style",
      "owner_id": "user_001",
      "industry": "fashion_retail",
      "business_type": "boutique",
      "size": "micro",
      "location": {
        "city": "São Paulo",
        "state": "SP",
        "region": "Southeast",
        "postal_code": "01234-567"
      },
      "contact": {
        "phone": "+5511999887766",
        "whatsapp": "+5511999887766",
        "email": "contato@boutiquefernanda.com.br",
        "website": "https://boutiquefernanda.com.br"
      },
      "social_media": {
        "instagram": "@boutiquefernanda",
        "facebook": "boutiquefernandasp",
        "tiktok": "@fernandastyleboutique"
      },
      "subscription": {
        "plan": "professional",
        "status": "active",
        "started_at": "2024-01-15T10:00:00Z",
        "billing_cycle": "monthly",
        "next_billing": "2024-12-15T10:00:00Z"
      },
      "departments_active": [
        "marketing",
        "customer_service", 
        "design",
        "sales_crm"
      ],
      "brand_config": {
        "primary_color": "#E91E63",
        "secondary_color": "#F8BBD9",
        "logo_url": "https://boutiquefernanda.com.br/logo.png",
        "brand_voice": "friendly_professional",
        "target_audience": "women_25_45_middle_class"
      }
    },
    {
      "id": "org_002",
      "name": "Pizzaria Bella Napoli",
      "owner_id": "user_002",
      "industry": "food_beverage",
      "business_type": "restaurant",
      "size": "small",
      "location": {
        "city": "Rio de Janeiro",
        "state": "RJ", 
        "region": "Southeast",
        "postal_code": "22222-333"
      },
      "contact": {
        "phone": "+5521987654321",
        "whatsapp": "+5521987654321",
        "email": "pedidos@bellanapoli.com.br",
        "website": "https://bellanapoli.com.br"
      },
      "social_media": {
        "instagram": "@pizzariabellanapoli",
        "facebook": "PizzariaBellaNapoliRJ"
      },
      "subscription": {
        "plan": "growth",
        "status": "active",
        "started_at": "2024-02-20T16:45:00Z",
        "billing_cycle": "monthly",
        "next_billing": "2024-12-20T16:45:00Z"
      },
      "departments_active": [
        "marketing",
        "customer_service",
        "finance"
      ],
      "brand_config": {
        "primary_color": "#D32F2F", 
        "secondary_color": "#4CAF50",
        "logo_url": "https://bellanapoli.com.br/logo.png",
        "brand_voice": "warm_traditional",
        "target_audience": "families_pizza_lovers_all_ages"
      }
    },
    {
      "id": "org_003",
      "name": "TechConsult Pro",
      "owner_id": "user_003",
      "industry": "professional_services",
      "business_type": "consulting",
      "size": "small",
      "location": {
        "city": "Belo Horizonte",
        "state": "MG",
        "region": "Southeast", 
        "postal_code": "30333-444"
      },
      "contact": {
        "phone": "+5531912345678",
        "whatsapp": "+5531912345678",
        "email": "contato@techconsultpro.com.br",
        "website": "https://techconsultpro.com.br"
      },
      "social_media": {
        "linkedin": "company/techconsult-pro",
        "instagram": "@techconsultpro"
      },
      "subscription": {
        "plan": "scale",
        "status": "active",
        "started_at": "2024-03-10T11:20:00Z",
        "billing_cycle": "annual",
        "next_billing": "2025-03-10T11:20:00Z"
      },
      "departments_active": [
        "marketing",
        "customer_service",
        "sales_crm",
        "data_bi",
        "finance"
      ],
      "brand_config": {
        "primary_color": "#1976D2",
        "secondary_color": "#FFA726", 
        "logo_url": "https://techconsultpro.com.br/logo.png",
        "brand_voice": "professional_expert",
        "target_audience": "business_owners_technology_decision_makers"
      }
    }
  ]
}
```

---

## 2. Marketing Department Test Data

### 2.1 Social Media Content
```json
// marketing_test_data.json
{
  "social_media_posts": [
    {
      "id": "post_001",
      "organization_id": "org_001",
      "platform": "instagram",
      "post_type": "image",
      "status": "published",
      "created_at": "2024-12-10T14:30:00Z",
      "published_at": "2024-12-10T19:00:00Z",
      "content": {
        "caption": "✨ Nova coleção chegando! Looks incríveis para você arrasar neste verão 🌞\n\nVem conferir as peças que são a cara da estação ☀️\n\n#ModaVerao #NovaColecao #Style #Fashion #BoutiqueFernanda",
        "hashtags": [
          "#modaverao", "#novacolecao", "#style", "#fashion", "#boutiquefernanda",
          "#roupasfemininas", "#lookdodia", "#tendencias"
        ],
        "call_to_action": "Vem conhecer na loja! 📍",
        "image_url": "https://example.com/images/nova-colecao-verao.jpg"
      },
      "metrics": {
        "impressions": 1247,
        "reach": 1089,
        "likes": 87,
        "comments": 12,
        "shares": 5,
        "saves": 23,
        "profile_visits": 34,
        "website_clicks": 8,
        "engagement_rate": 0.114
      }
    },
    {
      "id": "post_002", 
      "organization_id": "org_002",
      "platform": "instagram",
      "post_type": "video",
      "status": "published",
      "created_at": "2024-12-11T16:15:00Z",
      "published_at": "2024-12-11T20:30:00Z",
      "content": {
        "caption": "🍕 Massa fresquinha saindo do forno! Quem mais ama o cheiro de pizza no ar? \n\nNossa margherita é feita com amor e ingredientes selecionados 🇮🇹\n\nPedidos pelo WhatsApp: (21) 98765-4321",
        "hashtags": [
          "#pizza", "#pizzafresca", "#bellanapoli", "#massaartesanal", 
          "#pizzariario", "#delivery", "#italia", "#margherita"
        ],
        "call_to_action": "Peça já a sua! 🍕",
        "video_url": "https://example.com/videos/pizza-forno.mp4",
        "video_duration": 15
      },
      "metrics": {
        "impressions": 2156,
        "reach": 1834,
        "likes": 143,
        "comments": 28,
        "shares": 11,
        "saves": 8,
        "profile_visits": 67,
        "website_clicks": 23,
        "engagement_rate": 0.104,
        "video_views": 1654,
        "video_completion_rate": 0.78
      }
    }
  ],
  "content_calendar": [
    {
      "date": "2024-12-14",
      "posts": [
        {
          "time": "09:00",
          "platform": "instagram",
          "post_type": "stories",
          "content_theme": "behind_the_scenes",
          "status": "scheduled"
        },
        {
          "time": "19:00", 
          "platform": "instagram",
          "post_type": "feed",
          "content_theme": "product_showcase",
          "status": "draft"
        }
      ]
    },
    {
      "date": "2024-12-15",
      "posts": [
        {
          "time": "18:30",
          "platform": "facebook", 
          "post_type": "image",
          "content_theme": "customer_testimonial",
          "status": "idea"
        }
      ]
    }
  ]
}
```

### 2.2 Email Marketing Data
```json
// email_marketing_test_data.json
{
  "email_campaigns": [
    {
      "id": "email_001",
      "organization_id": "org_001",
      "name": "Promoção Black Friday - Boutique Fernanda",
      "type": "promotional",
      "status": "sent",
      "created_at": "2024-11-20T10:00:00Z",
      "sent_at": "2024-11-22T08:00:00Z",
      "subject_line": "🖤 Black Friday: Até 50% OFF em peças selecionadas!",
      "preview_text": "Não perca! Liquidação apenas hoje com os melhores descontos do ano",
      "recipients_count": 1247,
      "content": {
        "html_template": "black_friday_promo",
        "personalization": true,
        "cta_primary": "APROVEITAR DESCONTO",
        "cta_url": "https://boutiquefernanda.com.br/black-friday"
      },
      "metrics": {
        "delivered": 1189,
        "opened": 428,
        "clicked": 89,
        "unsubscribed": 3,
        "bounced": 58,
        "open_rate": 0.36,
        "click_rate": 0.075,
        "click_to_open_rate": 0.208,
        "unsubscribe_rate": 0.002
      }
    }
  ],
  "subscriber_segments": [
    {
      "id": "segment_001",
      "name": "VIP Customers",
      "organization_id": "org_001", 
      "criteria": {
        "purchase_history": "> R$ 500 últimos 6 meses",
        "engagement": "alta",
        "location": "São Paulo metro"
      },
      "subscriber_count": 156,
      "avg_open_rate": 0.42,
      "avg_click_rate": 0.089
    },
    {
      "id": "segment_002",
      "name": "New Subscribers",
      "organization_id": "org_001",
      "criteria": {
        "signup_date": "últimos 30 dias",
        "purchase_history": "nenhuma compra ainda"
      },
      "subscriber_count": 89,
      "avg_open_rate": 0.31,
      "avg_click_rate": 0.054
    }
  ]
}
```

---

## 3. Customer Service Test Data

### 3.1 WhatsApp Conversations
```json
// whatsapp_conversations.json
{
  "conversations": [
    {
      "id": "conv_001",
      "organization_id": "org_001",
      "customer": {
        "name": "Ana Beatriz",
        "phone": "+5511987654321",
        "customer_id": "cust_001"
      },
      "status": "resolved",
      "priority": "medium",
      "created_at": "2024-12-10T14:30:00Z",
      "resolved_at": "2024-12-10T15:45:00Z",
      "agent_type": "ai",
      "satisfaction_rating": 5,
      "messages": [
        {
          "id": "msg_001",
          "timestamp": "2024-12-10T14:30:00Z",
          "sender": "customer",
          "content": "Oi! Vocês têm vestido azul tamanho M em estoque?",
          "message_type": "text"
        },
        {
          "id": "msg_002",
          "timestamp": "2024-12-10T14:30:45Z",
          "sender": "ai_agent",
          "content": "Oi Ana! 😊 Tudo bem?\n\nSim, temos vestidos azuis tamanho M! Temos 3 modelos disponíveis:\n\n1️⃣ Vestido Midi Floral - R$ 89,90\n2️⃣ Vestido Longo Liso - R$ 119,90  \n3️⃣ Vestido Casual Jeans - R$ 79,90\n\nQual te interessou mais? Posso enviar as fotos! 📸",
          "message_type": "text",
          "ai_confidence": 0.95
        },
        {
          "id": "msg_003",
          "timestamp": "2024-12-10T14:35:00Z",
          "sender": "customer", 
          "content": "O vestido midi floral! Pode enviar foto?",
          "message_type": "text"
        },
        {
          "id": "msg_004",
          "timestamp": "2024-12-10T14:35:30Z",
          "sender": "ai_agent",
          "content": "Claro! Aqui está o Vestido Midi Floral azul tamanho M 💙",
          "message_type": "text"
        },
        {
          "id": "msg_005",
          "timestamp": "2024-12-10T14:35:35Z",
          "sender": "ai_agent",
          "content": "",
          "message_type": "image",
          "media_url": "https://example.com/products/vestido-midi-floral-azul-m.jpg",
          "ai_confidence": 0.98
        },
        {
          "id": "msg_006",
          "timestamp": "2024-12-10T14:40:00Z", 
          "sender": "customer",
          "content": "Perfeito! Quero comprar. Como faço?",
          "message_type": "text"
        },
        {
          "id": "msg_007",
          "timestamp": "2024-12-10T14:40:15Z",
          "sender": "ai_agent",
          "content": "Ótima escolha! 🎉\n\n📦 Vestido Midi Floral Azul - Tamanho M\n💰 Valor: R$ 89,90\n\n🚚 Entrega:\n• Retirada na loja: GRÁTIS\n• Entrega São Paulo: R$ 12,00 (1-2 dias)\n• Sedex: R$ 18,00 (1 dia)\n\n💳 Pagamento:\n• PIX (5% desconto): R$ 85,41\n• Cartão (até 3x): R$ 89,90\n\nConfirma o pedido? Digite SIM para finalizar! ✅",
          "message_type": "text",
          "ai_confidence": 0.92
        },
        {
          "id": "msg_008",
          "timestamp": "2024-12-10T14:42:00Z",
          "sender": "customer",
          "content": "SIM! Quero com entrega e vou pagar no PIX",
          "message_type": "text"
        },
        {
          "id": "msg_009",
          "timestamp": "2024-12-10T14:42:30Z",
          "sender": "ai_agent", 
          "content": "Perfeito Ana! 🎊\n\n✅ PEDIDO CONFIRMADO\n📋 Número: #BF2024001\n\n💰 Total: R$ 97,41\n(R$ 85,41 produto + R$ 12,00 entrega)\n\n🔑 PIX: Use a chave boutiquefernanda@pix.com.br\n\nAssim que confirmar o pagamento, prepararemos seu pedido com muito carinho! 💝\n\nPrecisa de mais alguma coisa?",
          "message_type": "text",
          "ai_confidence": 0.89
        }
      ],
      "tags": ["venda", "vestido", "pix", "entrega"],
      "resolution_category": "purchase_completed"
    },
    {
      "id": "conv_002",
      "organization_id": "org_002",
      "customer": {
        "name": "Roberto Silva",
        "phone": "+5521912345678", 
        "customer_id": "cust_002"
      },
      "status": "in_progress",
      "priority": "high",
      "created_at": "2024-12-12T19:15:00Z",
      "agent_type": "ai",
      "messages": [
        {
          "id": "msg_010",
          "timestamp": "2024-12-12T19:15:00Z",
          "sender": "customer",
          "content": "Boa noite! Fiz um pedido às 18h e ainda não chegou. Vocês disseram que seria em 40 min",
          "message_type": "text"
        },
        {
          "id": "msg_011", 
          "timestamp": "2024-12-12T19:15:30Z",
          "sender": "ai_agent",
          "content": "Boa noite Roberto! \n\nPeço desculpas pela demora! 😔\n\nVou verificar seu pedido agora mesmo. Pode me passar seu nome completo ou número do pedido?\n\nEnquanto isso, já vou adiantar que hoje tivemos um volume alto de pedidos devido à chuva, mas vamos resolver isso rapidinho! 🍕",
          "message_type": "text",
          "ai_confidence": 0.87
        },
        {
          "id": "msg_012",
          "timestamp": "2024-12-12T19:16:00Z",
          "sender": "customer", 
          "content": "Roberto Silva, pedido #BN789. Pedi 2 pizzas grandes",
          "message_type": "text"
        },
        {
          "id": "msg_013",
          "timestamp": "2024-12-12T19:16:45Z",
          "sender": "ai_agent",
          "content": "Encontrei seu pedido Roberto! 📋\n\n🍕 Pedido #BN789\n✅ 2 Pizzas Grandes (Margherita + Calabresa)\n⏰ Pedido: 18:03\n📍 Endereço: confirmado\n\nSua pizza SAIU DO FORNO agora e já está com nosso motoboy João! 🏍️\n\nTempo estimado: 15-20 minutos\n\nPor conta da demora, vou incluir uma coca-cola 2L de cortesia! 🥤\n\nDesculpa novamente pelo inconveniente! 🙏",
          "message_type": "text", 
          "ai_confidence": 0.91,
          "escalation_triggered": false
        }
      ],
      "tags": ["atraso", "entrega", "reclamacao"],
      "resolution_category": "delivery_delay"
    }
  ],
  "faq_responses": [
    {
      "id": "faq_001",
      "organization_id": "org_001",
      "question_pattern": "horario|funcionamento|aberto|fecha",
      "response": "📍 **HORÁRIO DE FUNCIONAMENTO**\n\n🕘 Segunda a Sexta: 9h às 19h\n🕘 Sábado: 9h às 18h  \n🕘 Domingo: 10h às 16h\n\n📍 Estamos na Rua das Flores, 123 - Vila Madalena\n\nVem nos visitar! 😊",
      "usage_count": 47,
      "success_rate": 0.94
    },
    {
      "id": "faq_002",
      "organization_id": "org_002", 
      "question_pattern": "entrega|delivery|prazo|demora",
      "response": "🚚 **INFORMAÇÕES DE ENTREGA**\n\n⏰ Prazo: 30-45 minutos\n📍 Área de entrega: até 5km da pizzaria\n💰 Taxa de entrega: R$ 8,00\n🆓 GRÁTIS acima de R$ 45,00\n\n📱 Acompanhe seu pedido em tempo real!\n\nFome de pizza? Estamos prontos! 🍕",
      "usage_count": 89,
      "success_rate": 0.91
    }
  ]
}
```

---

## 4. Sales & CRM Test Data

### 4.1 Leads & Opportunities
```json
// sales_crm_test_data.json
{
  "leads": [
    {
      "id": "lead_001",
      "organization_id": "org_001",
      "name": "Juliana Pereira",
      "email": "juliana.pereira@email.com",
      "phone": "+5511998877665",
      "source": "facebook_ads",
      "status": "qualified",
      "score": 85,
      "created_at": "2024-12-08T14:20:00Z",
      "last_interaction": "2024-12-10T16:30:00Z",
      "profile": {
        "age_range": "25-35",
        "location": "São Paulo, SP",
        "interests": ["moda", "fashion", "roupas femininas"],
        "purchase_intent": "high",
        "budget_estimate": "R$ 200-500"
      },
      "interactions": [
        {
          "date": "2024-12-08T14:20:00Z",
          "type": "form_submission",
          "details": "Preencheu formulário 'Catálogo Grátis'"
        },
        {
          "date": "2024-12-08T14:25:00Z", 
          "type": "whatsapp_message",
          "details": "Enviou mensagem perguntando sobre vestidos"
        },
        {
          "date": "2024-12-10T16:30:00Z",
          "type": "whatsapp_response",
          "details": "Respondeu interesse em agendar visita à loja"
        }
      ],
      "ai_insights": {
        "conversion_probability": 0.78,
        "recommended_approach": "agendar_visita_loja",
        "best_contact_time": "afternoons_weekdays",
        "product_recommendations": ["vestidos", "blusas_sociais"]
      }
    },
    {
      "id": "lead_002",
      "organization_id": "org_003",
      "name": "Marco Antonio Ribeiro",
      "email": "marco.ribeiro@empresa.com.br",
      "phone": "+5531987456123",
      "source": "linkedin",
      "status": "nurturing",
      "score": 62,
      "created_at": "2024-12-05T10:15:00Z",
      "last_interaction": "2024-12-12T09:45:00Z",
      "profile": {
        "company": "Indústria MR Ltda",
        "position": "Diretor de TI",
        "company_size": "50-100 funcionários",
        "location": "Belo Horizonte, MG",
        "budget_estimate": "R$ 5.000-15.000"
      },
      "interactions": [
        {
          "date": "2024-12-05T10:15:00Z",
          "type": "linkedin_connection",
          "details": "Aceitou conexão no LinkedIn"
        },
        {
          "date": "2024-12-07T14:30:00Z",
          "type": "content_engagement", 
          "details": "Curtiu e comentou post sobre automação"
        },
        {
          "date": "2024-12-12T09:45:00Z",
          "type": "email_open",
          "details": "Abriu email sobre consultoria em transformação digital"
        }
      ],
      "ai_insights": {
        "conversion_probability": 0.45,
        "recommended_approach": "educative_content",
        "best_contact_time": "mornings_tuesdays_thursdays",  
        "content_interests": ["automacao", "transformacao_digital", "consultoria_ti"]
      }
    }
  ],
  "opportunities": [
    {
      "id": "opp_001",
      "organization_id": "org_001",
      "lead_id": "lead_001", 
      "name": "Juliana P. - Guarda-roupa completo",
      "value": 450.00,
      "currency": "BRL",
      "stage": "proposal",
      "probability": 75,
      "expected_close_date": "2024-12-20T00:00:00Z",
      "created_at": "2024-12-10T16:30:00Z",
      "last_activity": "2024-12-12T11:15:00Z",
      "products_interested": [
        {
          "name": "Vestido Social Azul",
          "price": 120.00,
          "quantity": 1
        },
        {
          "name": "Blazer Feminino Preto", 
          "price": 180.00,
          "quantity": 1
        },
        {
          "name": "Calça Social Cinza",
          "price": 95.00, 
          "quantity": 1
        },
        {
          "name": "Blusa Seda Rosa",
          "price": 75.00,
          "quantity": 1
        }
      ],
      "stage_history": [
        {
          "stage": "lead",
          "date": "2024-12-08T14:20:00Z"
        },
        {
          "stage": "qualified",
          "date": "2024-12-10T16:30:00Z"
        },
        {
          "stage": "proposal",
          "date": "2024-12-12T11:15:00Z"
        }
      ],
      "next_actions": [
        "Enviar lookbook personalizado via WhatsApp",
        "Agendar prova de roupas na loja",
        "Oferecer desconto para pagamento à vista"
      ]
    }
  ],
  "sales_pipeline": [
    {
      "stage": "lead",
      "count": 23,
      "total_value": 5670.00,
      "conversion_rate": 0.65
    },
    {
      "stage": "qualified", 
      "count": 15,
      "total_value": 4890.00,
      "conversion_rate": 0.73
    },
    {
      "stage": "proposal",
      "count": 11,
      "total_value": 3560.00, 
      "conversion_rate": 0.68
    },
    {
      "stage": "negotiation",
      "count": 7,
      "total_value": 2340.00,
      "conversion_rate": 0.85
    },
    {
      "stage": "closed_won", 
      "count": 6,
      "total_value": 1890.00,
      "conversion_rate": 1.0
    }
  ]
}
```

---

## 5. Financial Test Data

### 5.1 Transactions & Cash Flow
```json
// finance_test_data.json
{
  "transactions": [
    {
      "id": "txn_001",
      "organization_id": "org_001",
      "date": "2024-12-10T14:30:00Z",
      "type": "income",
      "category": "product_sales",
      "description": "Venda Vestido Midi Floral - Ana Beatriz",
      "amount": 89.90,
      "currency": "BRL",
      "payment_method": "pix",
      "customer_id": "cust_001",
      "invoice_number": "BF2024001",
      "receipt_url": "https://example.com/receipts/bf2024001.pdf",
      "tax_info": {
        "gross_amount": 89.90,
        "tax_amount": 0.0,
        "net_amount": 89.90,
        "tax_category": "mei_exempt"
      },
      "ai_categorization": {
        "confidence": 0.98,
        "auto_categorized": true,
        "suggested_category": "product_sales"
      }
    },
    {
      "id": "txn_002",
      "organization_id": "org_001", 
      "date": "2024-12-11T09:15:00Z",
      "type": "expense",
      "category": "inventory",
      "description": "Compra tecidos - Fornecedor ABC Têxtil",
      "amount": -345.60,
      "currency": "BRL", 
      "payment_method": "card",
      "vendor": "ABC Têxtil Ltda",
      "receipt_url": "https://example.com/receipts/abc-textil-345.pdf",
      "tax_info": {
        "gross_amount": 345.60,
        "tax_amount": 41.47,
        "net_amount": 304.13,
        "tax_category": "icms_included"
      },
      "ai_categorization": {
        "confidence": 0.95,
        "auto_categorized": true,
        "suggested_category": "inventory"
      }
    },
    {
      "id": "txn_003",
      "organization_id": "org_002",
      "date": "2024-12-11T18:45:00Z", 
      "type": "income",
      "category": "food_sales", 
      "description": "Delivery - 2 Pizzas Grandes - Roberto Silva",
      "amount": 65.80,
      "currency": "BRL",
      "payment_method": "card",
      "customer_id": "cust_002",
      "order_number": "BN789",
      "tax_info": {
        "gross_amount": 65.80,
        "tax_amount": 0.0,
        "net_amount": 65.80, 
        "tax_category": "mei_exempt"
      },
      "ai_categorization": {
        "confidence": 0.99,
        "auto_categorized": true,
        "suggested_category": "food_sales"
      }
    }
  ],
  "monthly_summary": {
    "organization_id": "org_001",
    "period": "2024-12",
    "income": {
      "total": 12450.80,
      "categories": {
        "product_sales": 11230.50,
        "services": 1220.30
      }
    },
    "expenses": {
      "total": 7890.45,
      "categories": {
        "inventory": 4567.80,
        "marketing": 1234.50,
        "operations": 1456.70, 
        "administrative": 631.45
      }
    },
    "net_profit": 4560.35,
    "margin_percent": 36.6
  },
  "cash_flow_projection": [
    {
      "month": "2024-12",
      "projected_income": 12500.00,
      "projected_expenses": 8000.00,
      "net_cash_flow": 4500.00,
      "cumulative_balance": 18750.00
    },
    {
      "month": "2025-01", 
      "projected_income": 8900.00,
      "projected_expenses": 7200.00,
      "net_cash_flow": 1700.00,
      "cumulative_balance": 20450.00
    },
    {
      "month": "2025-02",
      "projected_income": 11200.00, 
      "projected_expenses": 7800.00,
      "net_cash_flow": 3400.00,
      "cumulative_balance": 23850.00
    }
  ]
}
```

---

## 6. Analytics & BI Test Data

### 6.1 Performance Metrics
```json
// analytics_test_data.json
{
  "kpi_dashboard": {
    "organization_id": "org_001",
    "period": "2024-12",
    "last_updated": "2024-12-13T10:00:00Z",
    "metrics": {
      "revenue": {
        "current": 12450.80,
        "previous": 10230.45,
        "growth_rate": 0.217,
        "target": 15000.00,
        "target_achievement": 0.83
      },
      "customers": {
        "total_active": 234,
        "new_customers": 45,
        "returning_customers": 189,
        "churn_rate": 0.08,
        "customer_lifetime_value": 456.78
      },
      "marketing": {
        "total_reach": 15670,
        "engagement_rate": 0.067,
        "cost_per_acquisition": 23.45,
        "return_on_ad_spend": 4.2
      },
      "operations": {
        "order_fulfillment_time": 1.2,
        "customer_satisfaction": 4.6,
        "inventory_turnover": 6.8,
        "gross_margin": 0.58
      }
    },
    "trends": {
      "revenue_trend": [
        {"month": "2024-08", "value": 8945.30},
        {"month": "2024-09", "value": 9456.70}, 
        {"month": "2024-10", "value": 10230.45},
        {"month": "2024-11", "value": 11567.80},
        {"month": "2024-12", "value": 12450.80}
      ],
      "customer_acquisition_trend": [
        {"month": "2024-08", "value": 28},
        {"month": "2024-09", "value": 32},
        {"month": "2024-10", "value": 38}, 
        {"month": "2024-11", "value": 42},
        {"month": "2024-12", "value": 45}
      ]
    }
  },
  "segment_analysis": {
    "customer_segments": [
      {
        "segment": "VIP",
        "criteria": "LTV > R$ 1000", 
        "count": 23,
        "percentage": 9.8,
        "avg_order_value": 345.67,
        "avg_frequency": 2.3,
        "satisfaction_score": 4.8
      },
      {
        "segment": "Regular",
        "criteria": "LTV R$ 300-1000",
        "count": 156,
        "percentage": 66.7,
        "avg_order_value": 178.45, 
        "avg_frequency": 1.8,
        "satisfaction_score": 4.5
      },
      {
        "segment": "New",
        "criteria": "< 3 months",
        "count": 55,
        "percentage": 23.5,
        "avg_order_value": 123.89,
        "avg_frequency": 1.2,
        "satisfaction_score": 4.3
      }
    ],
    "product_performance": [
      {
        "category": "Vestidos",
        "revenue": 5234.80,
        "units_sold": 67,
        "avg_price": 78.13,
        "margin": 0.62,
        "popularity_rank": 1
      },
      {
        "category": "Blusas", 
        "revenue": 3456.70,
        "units_sold": 89,
        "avg_price": 38.84,
        "margin": 0.58,
        "popularity_rank": 2
      },
      {
        "category": "Calças",
        "revenue": 2890.45,
        "units_sold": 34,
        "avg_price": 85.01,
        "margin": 0.54,
        "popularity_rank": 3
      }
    ]
  }
}
```

---

## 7. Integration Test Data

### 7.1 Third-Party Integration Mock Data
```json
// integration_test_data.json
{
  "whatsapp_business_api": {
    "webhook_events": [
      {
        "event_type": "message_received",
        "timestamp": "2024-12-13T10:30:00Z",
        "phone_number": "+5511987654321",
        "message": {
          "type": "text",
          "body": "Oi! Vocês têm promoção hoje?"
        },
        "contact": {
          "profile": {
            "name": "Maria Santos"
          }
        }
      },
      {
        "event_type": "message_status",
        "timestamp": "2024-12-13T10:31:00Z", 
        "message_id": "wamid.12345",
        "status": "delivered"
      }
    ],
    "message_templates": [
      {
        "name": "welcome_message",
        "language": "pt_BR",
        "status": "approved",
        "category": "utility",
        "components": [
          {
            "type": "body",
            "text": "Olá {{1}}! Bem-vindo(a) à {{2}}! Como podemos ajudar você hoje?"
          }
        ]
      }
    ]
  },
  "facebook_api": {
    "ad_accounts": [
      {
        "id": "act_123456789",
        "name": "Boutique Fernanda - Ads",
        "currency": "BRL",
        "timezone": "America/Sao_Paulo"
      }
    ],
    "campaigns": [
      {
        "id": "camp_001",
        "name": "Black Friday 2024", 
        "status": "ACTIVE",
        "objective": "CONVERSIONS",
        "daily_budget": 15000,
        "insights": {
          "impressions": 45670,
          "clicks": 1234,
          "spend": 45000,
          "conversions": 89
        }
      }
    ]
  },
  "instagram_basic_display": {
    "media": [
      {
        "id": "ig_media_001",
        "media_type": "IMAGE", 
        "media_url": "https://example.com/ig/image1.jpg",
        "caption": "Nova coleção chegando! ✨ #moda #style",
        "timestamp": "2024-12-10T19:00:00Z",
        "likes_count": 156,
        "comments_count": 23
      }
    ]
  },
  "mercado_pago": {
    "payments": [
      {
        "id": "mp_payment_001",
        "status": "approved",
        "transaction_amount": 89.90,
        "currency_id": "BRL",
        "date_created": "2024-12-10T14:35:00Z",
        "payment_method": {
          "type": "instant_payment",
          "id": "pix"
        },
        "payer": {
          "email": "ana.beatriz@email.com"
        },
        "external_reference": "BF2024001"
      }
    ]
  }
}
```

---

## 8. Load Testing Data

### 8.1 Performance Testing Scenarios
```yaml
# load_testing_scenarios.yaml
performance_testing:
  scenarios:
    basic_load:
      description: "Normal business day simulation"
      virtual_users: 50
      duration: "10 minutes"
      ramp_up: "30 seconds"
      operations:
        - whatsapp_message: 40%
        - social_media_post: 25%
        - dashboard_view: 20%
        - report_generation: 10%
        - settings_update: 5%
      
    peak_load:
      description: "Black Friday peak traffic simulation"
      virtual_users: 500
      duration: "30 minutes" 
      ramp_up: "2 minutes"
      operations:
        - whatsapp_message: 50%
        - order_processing: 30%
        - payment_confirmation: 15%
        - inventory_check: 5%
        
    stress_test:
      description: "Breaking point identification"
      virtual_users: 1000
      duration: "15 minutes"
      ramp_up: "1 minute"
      operations:
        - concurrent_ai_requests: 60%
        - database_queries: 25% 
        - file_uploads: 10%
        - report_exports: 5%

  performance_targets:
    response_time:
      api_endpoints: "< 200ms (95th percentile)"
      ai_generation: "< 10s (95th percentile)"
      database_queries: "< 100ms (95th percentile)"
      file_uploads: "< 5s (95th percentile)"
      
    throughput:
      whatsapp_messages: "100 msg/second"
      social_posts: "50 posts/second" 
      report_generation: "10 reports/second"
      concurrent_users: "500 active users"
      
    availability:
      uptime: "> 99.9%"
      error_rate: "< 0.1%" 
      timeout_rate: "< 0.05%"
```

### 8.2 Synthetic Data Generators
```python
# synthetic_data_generators.py
class BrazilianBusinessDataGenerator:
    def __init__(self):
        self.business_names = [
            "Boutique {}", "Loja {}", "Casa {}", "Ateliê {}", 
            "Studio {}", "Espaço {}", "Cantinho {}", "Empório {}"
        ]
        self.brazilian_names = [
            "Silva", "Santos", "Oliveira", "Souza", "Rodrigues",
            "Ferreira", "Alves", "Pereira", "Lima", "Gomes"
        ]
        self.brazilian_first_names = [
            "Maria", "Ana", "Fernanda", "Carla", "Juliana",
            "Carlos", "João", "Pedro", "Roberto", "Marcos"  
        ]
        self.cities = [
            ("São Paulo", "SP"), ("Rio de Janeiro", "RJ"), 
            ("Belo Horizonte", "MG"), ("Curitiba", "PR"),
            ("Fortaleza", "CE"), ("Recife", "PE"), ("Salvador", "BA")
        ]
    
    def generate_customer(self):
        """Generate realistic Brazilian customer data"""
        import random
        first_name = random.choice(self.brazilian_first_names)
        last_name = random.choice(self.brazilian_names) 
        city, state = random.choice(self.cities)
        
        return {
            "name": f"{first_name} {last_name}",
            "email": f"{first_name.lower()}.{last_name.lower()}@email.com",
            "phone": f"+55{random.randint(11, 99)}{random.randint(900000000, 999999999)}",
            "location": {"city": city, "state": state},
            "age": random.randint(18, 65),
            "income_bracket": random.choice(["C", "B2", "B1", "A"])
        }
    
    def generate_business(self):
        """Generate realistic Brazilian business data"""
        import random
        business_type = random.choice(["boutique", "restaurant", "salon", "store"])
        name_template = random.choice(self.business_names)
        owner_name = random.choice(self.brazilian_first_names)
        
        return {
            "name": name_template.format(owner_name),
            "type": business_type,
            "owner": self.generate_customer(),
            "revenue_range": random.choice([
                "0-5000", "5000-10000", "10000-25000", "25000-50000"
            ]),
            "employee_count": random.randint(1, 10),
            "years_operating": random.randint(1, 15)
        }
    
    def generate_transaction_batch(self, count=100):
        """Generate batch of realistic transactions"""
        import random
        from datetime import datetime, timedelta
        
        transactions = []
        for i in range(count):
            transaction_date = datetime.now() - timedelta(
                days=random.randint(0, 90)
            )
            
            transactions.append({
                "id": f"txn_{i:06d}",
                "date": transaction_date.isoformat(),
                "type": random.choice(["income", "expense"]),
                "amount": round(random.uniform(10.0, 500.0), 2),
                "category": random.choice([
                    "product_sales", "inventory", "marketing", 
                    "operations", "administrative"
                ]),
                "payment_method": random.choice([
                    "pix", "card", "cash", "bank_transfer"
                ])
            })
        
        return transactions
```

---

## 9. Error Scenarios & Edge Cases

### 9.1 Error Handling Test Cases
```json
// error_scenarios.json
{
  "api_errors": [
    {
      "scenario": "whatsapp_api_down",
      "error_type": "service_unavailable", 
      "http_status": 503,
      "response": {
        "error": "WhatsApp Business API temporarily unavailable",
        "retry_after": 300,
        "fallback_available": true
      },
      "expected_behavior": "Switch to email notification, queue messages for retry"
    },
    {
      "scenario": "ai_model_rate_limit",
      "error_type": "rate_limit_exceeded",
      "http_status": 429,
      "response": {
        "error": "Rate limit exceeded for OpenAI API",
        "retry_after": 60,
        "quota_remaining": 0
      },
      "expected_behavior": "Switch to backup model (Gemini), queue requests"
    },
    {
      "scenario": "payment_processing_error",
      "error_type": "payment_failed",
      "http_status": 400,
      "response": {
        "error": "Payment processing failed - insufficient funds",
        "transaction_id": "txn_failed_001",
        "customer_notified": false
      },
      "expected_behavior": "Notify customer, suggest alternative payment methods"
    }
  ],
  "data_validation_errors": [
    {
      "field": "phone_number",
      "invalid_values": [
        "+55119", // too short
        "+551199887766554433", // too long  
        "11998877665", // missing country code
        "phone", // non-numeric
        "" // empty
      ],
      "expected_error": "Invalid Brazilian phone number format"
    },
    {
      "field": "email",
      "invalid_values": [
        "invalid-email", // no @
        "@domain.com", // no local part
        "user@", // no domain
        "user space@domain.com", // spaces
        "" // empty
      ],
      "expected_error": "Invalid email address format"
    }
  ],
  "business_logic_errors": [
    {
      "scenario": "negative_inventory",
      "input": {
        "product_id": "prod_001",
        "quantity_requested": 10,
        "current_stock": 3
      },
      "expected_error": "Insufficient inventory - only 3 units available",
      "expected_behavior": "Suggest backorder or alternative products"
    },
    {
      "scenario": "invalid_discount",
      "input": {
        "discount_percentage": 150,
        "product_price": 100.00
      },
      "expected_error": "Discount cannot exceed 100%",
      "expected_behavior": "Cap discount at maximum allowed percentage"
    }
  ]
}
```

---

## 10. Testing Environment Configuration

### 10.1 Test Database Seeding
```sql
-- test_data_seed.sql
-- Comprehensive test data seeding for AI Departments Platform

-- Organizations
INSERT INTO organizations (id, name, industry, business_type, owner_email, created_at) VALUES
('org_001', 'Boutique Fernanda Style', 'fashion_retail', 'boutique', 'maria.silva@example.com', '2024-01-15 10:00:00'),
('org_002', 'Pizzaria Bella Napoli', 'food_beverage', 'restaurant', 'carlos.mendes@example.com', '2024-02-20 16:45:00'),
('org_003', 'TechConsult Pro', 'professional_services', 'consulting', 'fernanda.costa@example.com', '2024-03-10 11:20:00');

-- Users
INSERT INTO users (id, email, name, phone, organization_id, role, created_at) VALUES
('user_001', 'maria.silva@example.com', 'Maria Silva Santos', '+5511999887766', 'org_001', 'owner', '2024-01-15 10:00:00'),
('user_002', 'carlos.mendes@example.com', 'Carlos Eduardo Mendes', '+5521987654321', 'org_002', 'owner', '2024-02-20 16:45:00'), 
('user_003', 'fernanda.costa@example.com', 'Fernanda Costa Lima', '+5531912345678', 'org_003', 'owner', '2024-03-10 11:20:00');

-- Customers
INSERT INTO customers (id, organization_id, name, phone, email, created_at, lifetime_value) VALUES
('cust_001', 'org_001', 'Ana Beatriz Silva', '+5511987654321', 'ana.beatriz@email.com', '2024-11-15 14:30:00', 245.80),
('cust_002', 'org_002', 'Roberto Silva Santos', '+5521912345678', 'roberto.silva@email.com', '2024-10-22 18:15:00', 156.40),
('cust_003', 'org_001', 'Juliana Pereira Costa', '+5511998877665', 'juliana.pereira@email.com', '2024-12-08 14:20:00', 89.90);

-- Transactions
INSERT INTO transactions (id, organization_id, date, type, amount, category, description, payment_method) VALUES
('txn_001', 'org_001', '2024-12-10 14:30:00', 'income', 89.90, 'product_sales', 'Venda Vestido Midi Floral', 'pix'),
('txn_002', 'org_001', '2024-12-11 09:15:00', 'expense', -345.60, 'inventory', 'Compra tecidos - ABC Têxtil', 'card'),
('txn_003', 'org_002', '2024-12-11 18:45:00', 'income', 65.80, 'food_sales', 'Delivery 2 Pizzas Grandes', 'card');

-- Social Media Posts
INSERT INTO social_media_posts (id, organization_id, platform, content, status, scheduled_for, published_at) VALUES
('post_001', 'org_001', 'instagram', '{"caption": "✨ Nova coleção chegando!", "hashtags": ["#moda", "#style"]}', 'published', '2024-12-10 19:00:00', '2024-12-10 19:00:00'),
('post_002', 'org_002', 'instagram', '{"caption": "🍕 Massa fresquinha saindo do forno!", "hashtags": ["#pizza", "#delivery"]}', 'published', '2024-12-11 20:30:00', '2024-12-11 20:30:00');

-- Department Configurations  
INSERT INTO department_configs (organization_id, department_type, is_active, config_data) VALUES
('org_001', 'marketing', true, '{"brand_voice": "friendly_professional", "posting_frequency": "daily"}'),
('org_001', 'customer_service', true, '{"auto_response": true, "business_hours": "9-19"}'),
('org_002', 'marketing', true, '{"brand_voice": "warm_traditional", "posting_frequency": "daily"}'),
('org_002', 'customer_service', true, '{"auto_response": true, "business_hours": "17-23"}');
```

### 10.2 Automated Test Data Generation
```bash
#!/bin/bash
# generate_test_data.sh
# Automated script to populate test environment with realistic data

echo "🚀 Generating AI Departments Platform Test Data..."

# Generate customer data
echo "👥 Creating customer profiles..."
curl -X POST http://localhost:3000/api/test/generate-customers \
  -H "Content-Type: application/json" \
  -d '{"count": 100, "business_types": ["fashion", "food", "services"]}'

# Generate transaction history
echo "💰 Creating transaction history..."
curl -X POST http://localhost:3000/api/test/generate-transactions \
  -H "Content-Type: application/json" \
  -d '{"count": 500, "date_range": "90_days", "realistic_amounts": true}'

# Generate social media content
echo "📱 Creating social media posts..."
curl -X POST http://localhost:3000/api/test/generate-social-content \
  -H "Content-Type: application/json" \
  -d '{"count": 200, "platforms": ["instagram", "facebook"], "with_metrics": true}'

# Generate WhatsApp conversations  
echo "💬 Creating WhatsApp conversations..."
curl -X POST http://localhost:3000/api/test/generate-whatsapp-conversations \
  -H "Content-Type: application/json" \
  -d '{"count": 150, "include_ai_responses": true, "satisfaction_ratings": true}'

# Generate leads and opportunities
echo "🎯 Creating sales pipeline data..."
curl -X POST http://localhost:3000/api/test/generate-sales-data \
  -H "Content-Type: application/json" \
  -d '{"leads": 75, "opportunities": 25, "closed_deals": 15}'

echo "✅ Test data generation complete!"
echo "🔍 Access test dashboard at: http://localhost:3000/test-dashboard"
```

---

**Document Status:** Comprehensive test fixtures ready for development, testing, and demonstration environments. Includes realistic Brazilian business data while maintaining privacy compliance.