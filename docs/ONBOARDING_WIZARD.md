# Onboarding Wizard - "Company in Minutes"

## Overview

The onboarding wizard is designed to get micro-entrepreneurs from signup to their first business output in under 5 minutes. The wizard follows a "company in a box" approach, requiring minimal input while maximizing value generation through intelligent defaults and AI-powered automation.

## Core Principles

- **Minimal friction**: Maximum 5 questions to get started
- **Smart defaults**: AI infers missing information based on business segment
- **Immediate value**: Generate content preview before any integrations
- **Progressive disclosure**: Start simple, add complexity later

## Wizard Flow

### Step 1: Business Identity (60 seconds)
**Question 1: What type of business are you starting?**
- Dropdown with popular segments:
  - Food & Restaurants
  - Fashion & Clothing
  - Digital Services/Consulting
  - Beauty & Wellness
  - Local Services
  - E-commerce/Products
  - Content Creation
  - Professional Services
  - Custom (text input)

**Question 2: What's your business name?**
- Text input with real-time availability check
- AI suggestions if name is taken
- Option to "decide later" with temporary name

### Step 2: Brand Foundation (90 seconds)
**Logo & Colors Import:**
- Upload logo (auto-extract brand colors)
- OR select from AI-generated logo suggestions based on business type
- OR skip and use initials-based logo
- Color palette auto-generation if no logo provided

**Question 3: What's your brand tone?**
- Simple selector:
  - Professional & Trustworthy
  - Friendly & Approachable
  - Bold & Creative
  - Minimal & Modern
- AI will adapt all copy and content to this tone

### Step 3: Channels & Presence (90 seconds)
**Question 4: Where do you want to reach customers?**
- Multi-select checkboxes:
  - Instagram
  - Facebook
  - WhatsApp Business
  - Website/Blog
  - Email
  - TikTok
  - LinkedIn
- Based on selection, wizard prioritizes department setup

**Question 5: What's your main goal for the next 30 days?**
- Single select:
  - Get my first customers
  - Build brand awareness
  - Launch a product
  - Improve customer service
  - Test business ideas
- This drives initial content calendar and department focus

### Step 4: AI Generation & Preview (2 minutes)
**Instant Content Generation:**
Based on the 5 answers, AI generates:
- 7-day content calendar with posts
- Welcome message templates for WhatsApp
- Basic "About Us" copy
- 3 social media post designs
- Email welcome sequence

**Preview Interface:**
- Split screen showing generated content
- Toggle between different outputs
- Edit suggestions in real-time
- "Generate more options" button for variations

### Step 5: Activation Choice (30 seconds)
**Choose Your Starting Point:**
- **Start with Marketing** (default for brand awareness goal)
  - Activates Marketing Department
  - Schedules first week of posts
  - Sets up content calendar
- **Start with Customer Service** (default for customer-focused goals)
  - Activates Customer Experience Department
  - Sets up WhatsApp auto-responder
  - Creates FAQ knowledge base
- **Start with Both** (premium option)
  - Full activation of both departments
  - Integrated workflow setup

## Technical Implementation

### Data Collection Schema
```json
{
  "onboarding_session": {
    "session_id": "uuid",
    "user_id": "uuid",
    "business_segment": "string",
    "business_name": "string",
    "brand_assets": {
      "logo_url": "string",
      "primary_color": "#hex",
      "secondary_color": "#hex",
      "color_palette": ["#hex"]
    },
    "brand_tone": "professional|friendly|bold|minimal",
    "channels": ["instagram", "whatsapp", "email"],
    "primary_goal": "customers|awareness|launch|service|test",
    "generated_content": {
      "posts": [],
      "messages": [],
      "copy": {}
    },
    "department_choice": "marketing|cx|both",
    "completed_at": "timestamp"
  }
}
```

### AI Prompt Templates

**Content Generation Prompt:**
```
Generate a 7-day content strategy for a {business_segment} business named {business_name}.
Brand tone: {brand_tone}
Primary goal: {primary_goal}
Channels: {channels}

Requirements:
- Create engaging posts appropriate for each channel
- Include both educational and promotional content
- Maintain consistent brand voice
- Include call-to-actions aligned with the primary goal
- Consider the target audience for {business_segment}

Output format: JSON with posts array containing platform, content, timing, and hashtags.
```

**Logo Generation Prompt:**
```
Design a minimal, modern logo concept for {business_name}, a {business_segment} business.
Style preferences: {brand_tone}
Requirements:
- Simple and scalable
- Works in single color
- Memorable and professional
- Appropriate for digital use
Generate 3 variations with different approaches.
```

### Integration Points

**Logo Processing:**
- File upload handler (max 5MB, PNG/JPG/SVG)
- AI color extraction using computer vision
- Auto-generation of favicon and social media variants
- Brand guideline creation

**Content Preview System:**
- Real-time rendering of generated posts
- Mock social media feed display
- WhatsApp conversation simulator
- Email template preview

**Department Activation:**
- Automatic agent configuration based on selections
- Template application for chosen business segment
- Initial workflow setup
- Integration credential prompts (defer to post-onboarding)

## Success Metrics

### Completion Metrics
- Wizard completion rate (target: >80%)
- Time to completion (target: <5 minutes)
- Drop-off points identification

### Activation Metrics
- First content published within 24 hours (target: >60%)
- Department utilization within first week (target: >70%)
- User return within 7 days (target: >50%)

### Quality Metrics
- User satisfaction with generated content (target: >4/5)
- Edit rate of generated content (target: <30%)
- Template usage vs. custom creation

## Error Handling & Fallbacks

### Technical Failures
- Auto-save progress at each step
- Graceful degradation for AI failures
- Fallback to manual input options
- Clear error messages with next steps

### User Experience Failures
- "Skip for now" option on all non-essential fields
- Ability to go back and modify previous answers
- Exit and resume later functionality
- Help tooltips for complex questions

## Mobile Optimization

### Responsive Design
- Single column layout for mobile
- Large, touch-friendly buttons
- Optimized image upload flow
- Swipe gestures for navigation

### Performance
- Progressive loading of content
- Offline capability for basic flow
- Compressed image processing
- Minimal JavaScript bundles

## Post-Onboarding Experience

### Immediate Next Steps
- Dashboard tour highlighting key features
- First task suggestions based on department choice
- Calendar view of scheduled content
- Integration setup prompts (non-blocking)

### Progressive Enhancement
- Advanced features unlock after basic usage
- Tutorial system for complex workflows
- Achievement system for milestones
- Upgrade prompts at natural moments

## A/B Testing Framework

### Test Variations
- Question order and wording
- Number of questions (3 vs 5 vs 7)
- Preview vs. no preview flow
- Default selections based on segment

### Success Criteria
- Completion rate
- Time to first value
- Long-term engagement
- Revenue conversion

## Implementation Phases

### Phase 1: Core Wizard (MVP)
- Basic 5-question flow
- Simple content generation
- Department activation
- Mobile-responsive design

### Phase 2: Enhanced Preview
- Rich content preview
- Real-time editing
- Multiple generation options
- Brand asset processing

### Phase 3: Intelligence Layer
- Smart defaults based on similar businesses
- Predictive channel recommendations
- Automated A/B testing
- Advanced personalization

## Integration Dependencies

### Required Services
- AI content generation API
- Image processing service
- Brand color extraction
- Template rendering engine

### Optional Enhancements
- Industry database for smart defaults
- Competitive analysis for suggestions
- Social media preview APIs
- Domain availability checker

This onboarding wizard transforms the traditional complex business setup into a streamlined, AI-powered experience that generates immediate value while collecting the minimum viable information needed to create a functional business presence.