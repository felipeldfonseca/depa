# Figma Design Assets & Links
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Felipe PM + Claude  
**Status:** DESIGN SYSTEM READY  

---

## Overview

Comprehensive Figma design system and asset library for the AI Departments Platform. This document organizes all design assets, prototypes, and resources needed for development and brand consistency.

**Design Philosophy:**
- Mobile-first responsive design
- Brazilian market aesthetics
- Accessibility compliant (WCAG 2.1 AA)
- Component-based design system
- Consistent brand experience across all touchpoints

---

## 1. Master Design System

### 1.1 Design System Foundation
```yaml
figma_design_system:
  main_file: 
    name: "AI Departments Platform - Design System"
    url: "https://figma.com/file/ai-departments-design-system"
    description: "Master design system with all components, tokens, and guidelines"
    last_updated: "2024-12-13"
    
  components_included:
    foundations:
      - "Color palette and semantic tokens"
      - "Typography scale and font families" 
      - "Spacing and layout grids"
      - "Border radius and shadow styles"
      - "Icon library and illustration style"
      
    components:
      - "Button variants and states"
      - "Form elements (inputs, selects, checkboxes)"
      - "Navigation components"
      - "Card layouts and content blocks"
      - "Modal and overlay patterns"
      - "Data visualization elements"
      
    patterns:
      - "Dashboard layouts"
      - "Onboarding flows"
      - "Department management interfaces"  
      - "Mobile-responsive patterns"
      - "Error and empty states"

  brand_guidelines:
    primary_colors:
      brand_primary: "#6366F1" # Indigo-500
      brand_secondary: "#F59E0B" # Amber-500
      success: "#10B981" # Emerald-500
      warning: "#F59E0B" # Amber-500  
      error: "#EF4444" # Red-500
      info: "#3B82F6" # Blue-500
      
    typography:
      heading_font: "Inter Bold"
      body_font: "Inter Regular"  
      code_font: "JetBrains Mono"
      
    spacing_scale: "[4, 8, 12, 16, 24, 32, 48, 64, 96, 128]px"
    
  accessibility:
    color_contrast: "All color combinations meet WCAG AA standards"
    focus_states: "Clear focus indicators for keyboard navigation"
    touch_targets: "Minimum 44px touch targets for mobile"
    semantic_markup: "Proper heading hierarchy and ARIA labels"
```

### 1.2 Component Library
```yaml
component_library:
  url: "https://figma.com/file/ai-departments-components"
  description: "Production-ready component library with variants and properties"
  
  core_components:
    buttons:
      variants: ["primary", "secondary", "tertiary", "danger"]
      sizes: ["small", "medium", "large"]  
      states: ["default", "hover", "active", "disabled", "loading"]
      
    form_controls:
      text_input:
        variants: ["default", "error", "success"]
        sizes: ["small", "medium", "large"]
        states: ["empty", "filled", "focused", "disabled"]
      
      select_dropdown:
        variants: ["single", "multi", "searchable"]
        states: ["closed", "open", "loading", "error"]
        
      checkbox_radio:
        types: ["checkbox", "radio", "toggle"]
        states: ["unchecked", "checked", "indeterminate", "disabled"]
        
    navigation:
      top_navigation:
        variants: ["desktop", "tablet", "mobile"]
        states: ["collapsed", "expanded", "with_notifications"]
        
      side_navigation:
        variants: ["full", "collapsed", "overlay"]
        categories: ["departments", "settings", "help"]
        
      breadcrumbs:
        max_items: 5
        truncation: "middle_ellipsis"
        
    data_display:
      cards:
        variants: ["basic", "with_image", "with_actions", "stats"]
        elevations: ["none", "low", "medium", "high"]
        
      tables:
        variants: ["basic", "sortable", "filterable", "pagination"]
        row_actions: ["view", "edit", "delete", "duplicate"]
        
      charts:
        types: ["line", "bar", "pie", "area", "scatter"]
        themes: ["light", "dark", "brand"]
        
    feedback:
      alerts:
        types: ["info", "success", "warning", "error"]
        variants: ["banner", "inline", "toast"]
        dismissible: true
        
      loading_states:
        types: ["spinner", "skeleton", "progress_bar"]
        sizes: ["small", "medium", "large", "full_page"]
        
      empty_states:
        contexts: ["no_data", "no_results", "error", "welcome"]
        with_actions: ["primary_cta", "secondary_cta", "help_link"]
```

---

## 2. Application Screens & Flows

### 2.1 Core Application Screens
```yaml
main_application_screens:
  desktop_app:
    url: "https://figma.com/file/ai-departments-desktop-app"
    description: "Complete desktop application screens and flows"
    
    key_screens:
      onboarding:
        - "Welcome and value proposition"
        - "Business information setup"
        - "Department selection wizard"
        - "Integration connections"
        - "First success moment"
        
      dashboard:
        - "Executive overview dashboard"
        - "Department performance tiles"
        - "Recent activity feed"
        - "Quick action shortcuts"
        - "Notification center"
        
      department_management:
        - "Marketing department interface"
        - "Customer service department interface"  
        - "Sales CRM department interface"
        - "Finance department interface"
        - "Design department interface"
        - "Data & BI department interface"
        - "Growth & Ads department interface"
        
      settings_configuration:
        - "Business profile settings"
        - "Brand configuration"
        - "Integration management"
        - "Billing and subscription"
        - "Team and permissions"
        - "Notification preferences"
        
      content_creation:
        - "Social media post creator"
        - "Email campaign builder"  
        - "WhatsApp message templates"
        - "Design asset generator"
        - "Report builder"
        
  mobile_responsive:
    url: "https://figma.com/file/ai-departments-mobile"
    description: "Mobile-optimized responsive layouts"
    
    breakpoints:
      mobile: "320px - 767px"
      tablet: "768px - 1023px" 
      desktop: "1024px+"
      
    mobile_specific_patterns:
      - "Bottom tab navigation"
      - "Swipe gestures for content"
      - "Collapsible sections"
      - "Touch-optimized controls"
      - "Mobile-first forms"
```

### 2.2 User Journey Prototypes
```yaml
interactive_prototypes:
  new_user_onboarding:
    url: "https://figma.com/proto/new-user-onboarding"
    description: "Complete new user onboarding experience"
    flow_steps:
      1: "Landing page → Sign up"
      2: "Business information collection"
      3: "Department selection and explanation"
      4: "Integration setup (WhatsApp, Social)"
      5: "First content generation"
      6: "Success confirmation and next steps"
    testing_scenarios:
      - "Fashion boutique owner setup"
      - "Restaurant owner setup"
      - "Service business owner setup"
      
  daily_usage_flows:
    url: "https://figma.com/proto/daily-usage-flows"
    description: "Common daily tasks and workflows"
    key_flows:
      - "Morning dashboard check"
      - "Respond to WhatsApp messages"
      - "Create and schedule social post"
      - "Review performance metrics"  
      - "Adjust campaign settings"
      
  department_deep_dives:
    marketing_department:
      url: "https://figma.com/proto/marketing-department"
      flows: ["Content calendar", "Social media scheduling", "Performance analytics"]
      
    customer_service:
      url: "https://figma.com/proto/customer-service"
      flows: ["WhatsApp management", "FAQ updates", "Response templates"]
      
    sales_crm:
      url: "https://figma.com/proto/sales-crm"
      flows: ["Lead management", "Pipeline overview", "Follow-up automation"]
```

---

## 3. Marketing & Brand Assets

### 3.1 Brand Identity Assets
```yaml
brand_identity:
  logo_variations:
    url: "https://figma.com/file/ai-departments-brand-identity"
    description: "Complete brand identity system"
    
    logo_files:
      primary_logo:
        - "Horizontal layout (default)"
        - "Vertical/stacked layout"
        - "Icon mark only"
        - "Text mark only"
        
      color_variations:
        - "Full color (primary)"
        - "Single color (white)"
        - "Single color (black)"
        - "Grayscale"
        
      file_formats:
        vector: ["SVG", "AI", "PDF"]
        raster: ["PNG (transparent)", "JPG", "WebP"]
        sizes: ["16px", "32px", "64px", "128px", "256px", "512px", "Original"]
        
  brand_applications:
    business_cards: "Standard and digital formats"
    letterhead: "Corporate communication template"
    presentation_template: "Branded slide deck template"
    social_media_templates: "Profile covers, post templates"
    merchandise_mockups: "T-shirts, stickers, notebooks"
    
  brand_guidelines:
    logo_usage: "Minimum sizes, clear space, don'ts"
    color_specifications: "Hex, RGB, CMYK, Pantone values"
    typography_hierarchy: "Heading styles, body text, captions"
    imagery_style: "Photography style, illustration approach"
    voice_and_tone: "Writing guidelines, messaging framework"
```

### 3.2 Marketing Materials
```yaml
marketing_materials:
  website_designs:
    landing_page:
      url: "https://figma.com/file/landing-page-design"
      variations: ["Version A", "Version B (A/B test)"]
      sections: ["Hero", "Features", "Pricing", "Testimonials", "CTA"]
      
    product_pages:
      url: "https://figma.com/file/product-pages"
      pages: ["Features overview", "Department details", "Pricing", "Use cases"]
      
  social_media_assets:
    url: "https://figma.com/file/social-media-templates"
    platforms:
      instagram:
        - "Feed post templates (1080x1080)"
        - "Stories templates (1080x1920)"  
        - "IGTV covers (1080x1350)"
        - "Highlight covers (161x161)"
        
      linkedin:
        - "Company page banner (1192x220)"
        - "Post templates (1200x1200)"
        - "Article headers (1200x627)"
        
      facebook:
        - "Cover photo (851x315)"
        - "Post templates (1200x630)"
        - "Event covers (1920x1080)"
        
  print_materials:
    url: "https://figma.com/file/print-materials"
    items:
      - "Brochures and flyers"
      - "Trade show booth graphics"  
      - "Conference presentation templates"
      - "Partner materials and co-marketing assets"
```

---

## 4. Product Screenshots & Documentation

### 4.1 Feature Documentation Visuals
```yaml
product_documentation:
  feature_screenshots:
    url: "https://figma.com/file/product-screenshots"
    description: "High-quality product screenshots for documentation"
    
    organized_by_department:
      marketing:
        - "Social media calendar interface"
        - "Post creation workflow"
        - "Analytics dashboard"
        - "Content library management"
        
      customer_service:
        - "WhatsApp conversation interface"
        - "Auto-response configuration"
        - "FAQ management"
        - "Customer satisfaction reports"
        
      sales_crm:
        - "Lead pipeline visualization"
        - "Contact management" 
        - "Deal tracking interface"
        - "Sales performance metrics"
        
  tutorial_illustrations:
    url: "https://figma.com/file/tutorial-illustrations" 
    description: "Step-by-step visual guides for user education"
    
    tutorial_series:
      getting_started:
        - "Account setup walkthrough"
        - "First department activation"
        - "Integration connection guide"
        - "Publishing first content"
        
      advanced_features:
        - "Advanced automation setup"
        - "Custom template creation"
        - "Analytics deep dive"
        - "Team collaboration features"
        
  help_documentation:
    url: "https://figma.com/file/help-documentation"
    description: "Visual aids for help articles and FAQ"
    
    visual_aids:
      - "Annotated interface screenshots"
      - "Process flow diagrams"
      - "Before/after comparisons"
      - "Troubleshooting visual guides"
```

### 4.2 Case Study & Success Story Assets
```yaml
case_study_assets:
  customer_success_stories:
    url: "https://figma.com/file/customer-success-stories"
    description: "Visual assets for customer success narratives"
    
    story_formats:
      infographic_style:
        - "Before/after metrics visualization"
        - "Timeline of improvements"
        - "ROI calculation graphics"
        - "Key quote highlights"
        
      photo_story_format:
        - "Customer photo + business context"
        - "Product screenshots in use"
        - "Results dashboard captures"
        - "Testimonial quote overlays"
        
  industry_use_cases:
    url: "https://figma.com/file/industry-use-cases"
    industries:
      fashion_retail:
        - "Boutique social media transformation"
        - "Inventory management improvement"
        - "Customer engagement increase"
        
      food_beverage:
        - "Restaurant delivery optimization"
        - "Menu performance analytics"
        - "Customer service automation"
        
      professional_services:
        - "Consultant lead generation improvement"
        - "Client communication streamlining"  
        - "Business development automation"
```

---

## 5. Development Handoff Resources

### 5.1 Design Tokens & Assets Export
```yaml
design_tokens:
  figma_tokens_plugin:
    url: "https://figma.com/file/design-tokens-export"
    description: "Design tokens exported for development consumption"
    
    token_categories:
      colors:
        format: "JSON, CSS custom properties, SCSS variables"
        includes: "Semantic color definitions, theme variations"
        
      typography:
        format: "CSS font definitions, React component props"
        includes: "Font families, sizes, weights, line heights"
        
      spacing:
        format: "Tailwind spacing scale, CSS utilities"
        includes: "Margin, padding, gap values"
        
      shadows:
        format: "CSS box-shadow definitions"
        includes: "Elevation levels, focus states"
        
  asset_export_specifications:
    icons:
      format: "SVG (optimized)"
      naming_convention: "icon-{name}-{size}.svg"
      sizes: ["16px", "20px", "24px", "32px"]
      
    images:
      format: "PNG, WebP (with fallback)"
      naming_convention: "img-{context}-{description}.{ext}"
      optimization: "Compressed, responsive sizes"
      
    illustrations:
      format: "SVG (for scalability)"
      naming_convention: "illust-{context}-{name}.svg"
      variations: "Light theme, dark theme variants"
```

### 5.2 Component Specifications  
```yaml
component_specifications:
  figma_to_code:
    url: "https://figma.com/file/component-specifications"
    description: "Detailed specifications for developer implementation"
    
    specification_includes:
      measurements:
        - "Exact pixel dimensions"
        - "Spacing between elements"
        - "Border radius values"
        - "Typography specifications"
        
      interactions:
        - "Hover state definitions"
        - "Click/tap animations"
        - "Loading state behaviors"
        - "Error state handling"
        
      responsive_behavior:
        - "Breakpoint-specific layouts"
        - "Flex/grid specifications"
        - "Mobile touch targets"
        - "Tablet adaptations"
        
  implementation_notes:
    accessibility: "ARIA labels, keyboard navigation, screen reader support"
    performance: "Image optimization, lazy loading, animation considerations"
    browser_support: "Cross-browser compatibility notes"
    testing: "Visual regression test cases"
```

---

## 6. Collaboration & Version Control

### 6.1 Team Access & Permissions
```yaml
team_collaboration:
  figma_team_setup:
    team_name: "AI Departments Platform Design"
    subscription: "Figma Professional"
    
    team_members:
      design_lead:
        role: "Admin"
        permissions: "Full access to all files and team settings"
        
      developers:
        role: "Developer"
        permissions: "View and inspect designs, export assets"
        
      product_managers:
        role: "Editor"
        permissions: "Comment and suggest changes"
        
      stakeholders:
        role: "Viewer"
        permissions: "View designs and leave comments"
        
  collaboration_workflow:
    design_reviews:
      frequency: "Weekly design review meetings"
      process: "Figma comments → Discussion → Approval"
      documentation: "Design decisions logged in Figma"
      
    handoff_process:
      developer_inspection: "Dev mode for precise measurements"
      asset_delivery: "Automated export via Figma API"
      specification_updates: "Version-controlled spec updates"
```

### 6.2 Version Management
```yaml
version_control:
  file_naming_convention:
    format: "[Project] - [Component/Screen] - v[X.Y]"
    examples:
      - "AI Departments - Design System - v2.1"
      - "AI Departments - Dashboard - v1.3"
      - "AI Departments - Mobile App - v1.0"
      
  version_history:
    major_versions: "Breaking changes, new features"
    minor_versions: "Component updates, refinements"
    patch_versions: "Bug fixes, small adjustments"
    
  backup_strategy:
    figma_version_history: "Automatic Figma versioning"
    external_backup: "Weekly export to design repository"
    documentation_sync: "Design updates trigger doc updates"
```

---

## 7. Quick Access Links

### 7.1 Essential Design Resources
```markdown
# 🎨 Quick Access - Design Resources

## 📋 Master Files
- [Design System](https://figma.com/file/ai-departments-design-system) - Core design foundation
- [Component Library](https://figma.com/file/ai-departments-components) - Production components
- [Desktop App](https://figma.com/file/ai-departments-desktop-app) - Main application screens

## 📱 Mobile & Responsive  
- [Mobile Responsive](https://figma.com/file/ai-departments-mobile) - Mobile-optimized layouts
- [Tablet Adaptations](https://figma.com/file/ai-departments-tablet) - Tablet-specific designs

## 🎯 Brand & Marketing
- [Brand Identity](https://figma.com/file/ai-departments-brand-identity) - Logo, colors, guidelines
- [Marketing Materials](https://figma.com/file/marketing-materials) - Social media, print assets
- [Website Designs](https://figma.com/file/landing-page-design) - Landing pages, product pages

## 🚀 Prototypes & Flows
- [Onboarding Flow](https://figma.com/proto/new-user-onboarding) - New user journey
- [Daily Usage](https://figma.com/proto/daily-usage-flows) - Common workflows
- [Department Deep Dives](https://figma.com/proto/marketing-department) - Feature-specific flows

## 📊 Documentation Assets
- [Product Screenshots](https://figma.com/file/product-screenshots) - Feature documentation
- [Tutorial Illustrations](https://figma.com/file/tutorial-illustrations) - User education
- [Success Stories](https://figma.com/file/customer-success-stories) - Case study assets

## 🛠️ Development Resources
- [Design Tokens](https://figma.com/file/design-tokens-export) - Exported tokens for dev
- [Component Specs](https://figma.com/file/component-specifications) - Implementation details
- [Asset Library](https://figma.com/file/asset-library) - Icons, illustrations, images
```

### 7.2 Design System Quick Reference
```yaml
design_system_quick_ref:
  colors:
    primary: "#6366F1"
    secondary: "#F59E0B"
    success: "#10B981"
    warning: "#F59E0B"
    error: "#EF4444"
    info: "#3B82F6"
    
  typography:
    headings: "Inter Bold (32px, 24px, 20px, 18px, 16px)"
    body: "Inter Regular (16px, 14px)"
    small: "Inter Regular (12px)"
    
  spacing: "4px base unit (4, 8, 12, 16, 24, 32, 48, 64, 96, 128)"
  
  border_radius: "4px (small), 8px (medium), 12px (large), 16px (xl)"
  
  breakpoints:
    mobile: "320px"
    tablet: "768px" 
    desktop: "1024px"
    wide: "1440px"
```

---

**Document Status:** Complete Figma design system and asset organization ready for development handoff and team collaboration. All links are placeholder examples - replace with actual Figma file URLs when created.