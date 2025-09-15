# Design System - AI Departments Platform

**Version:** 1.0  
**Last Updated:** 2025-09-13  
**Owner:** Claude  
**Status:** DRAFT  

---

## 1. Overview

The AI Departments Platform design system follows a **neo-futuristic, dark-mode-first** approach that balances cutting-edge aesthetics with pragmatic usability. The system emphasizes clarity, trust, and professional competence while maintaining an inspiring, modern feel.

### Design Philosophy
- **Neo-futuristic but pragmatic**: Modern, sleek interfaces without sacrificing usability
- **Dark mode first**: Optimized for extended use and professional environments
- **Trust through clarity**: Clean layouts that build confidence in AI-powered tools
- **Rapid success**: Users should achieve results in 1-2 clicks

---

## 2. Design Tokens

### 2.1 Color Palette

#### Primary Colors (Dark Mode)
```css
/* Surface Colors */
--surface-primary: #0a0a0b;     /* Main background */
--surface-secondary: #131316;   /* Card backgrounds */
--surface-tertiary: #1c1c21;    /* Elevated elements */
--surface-border: #2a2a30;      /* Borders and dividers */

/* Text Colors */
--text-primary: #f8f9fa;        /* Primary text */
--text-secondary: #b8bcc8;      /* Secondary text */
--text-tertiary: #6c757d;       /* Tertiary text, hints */
--text-inverse: #0a0a0b;        /* Text on light backgrounds */

/* Brand Colors */
--brand-primary: #6366f1;       /* Primary actions, links */
--brand-secondary: #8b5cf6;     /* Secondary accents */
--brand-tertiary: #06b6d4;      /* Tertiary accents */

/* Semantic Colors */
--success: #10b981;             /* Success states */
--warning: #f59e0b;             /* Warning states */
--error: #ef4444;               /* Error states */
--info: #3b82f6;                /* Information states */

/* Department Colors */
--dept-marketing: #ff6b6b;      /* Marketing department */
--dept-customer: #4ecdc4;       /* Customer service */
--dept-design: #ff8a65;         /* Design department */
--dept-dev: #ab47bc;            /* Development */
--dept-data: #42a5f5;           /* Data & BI */
--dept-sales: #66bb6a;          /* Sales & CRM */
```

#### Light Mode Variants (Future Support)
```css
/* Surface Colors (Light) */
--surface-primary-light: #ffffff;
--surface-secondary-light: #f8f9fa;
--surface-tertiary-light: #e9ecef;
--surface-border-light: #dee2e6;

/* Text Colors (Light) */
--text-primary-light: #212529;
--text-secondary-light: #495057;
--text-tertiary-light: #6c757d;
```

### 2.2 Typography

#### Font Stack
```css
/* Primary Font - Inter */
font-family: 'Inter', 'SF Pro Display', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;

/* Monospace Font - JetBrains Mono */
font-family-mono: 'JetBrains Mono', 'SF Mono', Monaco, 'Cascadia Code', monospace;
```

#### Type Scale
```css
/* Headings */
--text-4xl: 2.5rem;    /* 40px - Page titles */
--text-3xl: 2rem;      /* 32px - Section titles */
--text-2xl: 1.5rem;    /* 24px - Card titles */
--text-xl: 1.25rem;    /* 20px - Subsection titles */
--text-lg: 1.125rem;   /* 18px - Large body text */

/* Body Text */
--text-base: 1rem;     /* 16px - Default body text */
--text-sm: 0.875rem;   /* 14px - Small text */
--text-xs: 0.75rem;    /* 12px - Captions, labels */

/* Line Heights */
--leading-tight: 1.25;
--leading-normal: 1.5;
--leading-relaxed: 1.75;
```

#### Font Weights
```css
--font-light: 300;
--font-normal: 400;
--font-medium: 500;
--font-semibold: 600;
--font-bold: 700;
```

### 2.3 Spacing System

#### Spacing Scale (8px base unit)
```css
--space-0: 0;          /* 0px */
--space-1: 0.25rem;    /* 4px */
--space-2: 0.5rem;     /* 8px */
--space-3: 0.75rem;    /* 12px */
--space-4: 1rem;       /* 16px */
--space-5: 1.25rem;    /* 20px */
--space-6: 1.5rem;     /* 24px */
--space-8: 2rem;       /* 32px */
--space-10: 2.5rem;    /* 40px */
--space-12: 3rem;      /* 48px */
--space-16: 4rem;      /* 64px */
--space-20: 5rem;      /* 80px */
--space-24: 6rem;      /* 96px */
```

### 2.4 Border Radius

#### Radius Scale
```css
--radius-none: 0;
--radius-sm: 0.25rem;   /* 4px - Small elements */
--radius-md: 0.5rem;    /* 8px - Default radius */
--radius-lg: 0.75rem;   /* 12px - Cards, panels */
--radius-xl: 1rem;      /* 16px - Large cards */
--radius-2xl: 1.5rem;   /* 24px - Major sections */
--radius-full: 9999px;  /* Fully rounded */
```

### 2.5 Shadows

#### Shadow System
```css
/* Subtle depth for cards */
--shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.2);

/* Default card shadow */
--shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.3), 
             0 2px 4px -1px rgba(0, 0, 0, 0.2);

/* Elevated elements */
--shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.3), 
             0 4px 6px -2px rgba(0, 0, 0, 0.2);

/* Modal overlays */
--shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.3), 
             0 10px 10px -5px rgba(0, 0, 0, 0.2);

/* Glowing brand elements */
--shadow-brand: 0 0 20px rgba(99, 102, 241, 0.4);
```

---

## 3. Core Components

### 3.1 AppShell

#### Structure
```tsx
interface AppShellProps {
  children: React.ReactNode;
  showSidebar?: boolean;
  company?: Company;
  user: User;
}
```

#### Layout Specifications
- **Header Height**: 64px (--space-16)
- **Sidebar Width**: 280px (collapsed: 64px)
- **Main Content**: Fluid with max-width constraints
- **Footer**: Optional, contextual

#### Responsive Behavior
- **Desktop (>1024px)**: Full sidebar + header
- **Tablet (768-1024px)**: Collapsible sidebar
- **Mobile (<768px)**: Bottom navigation + hamburger menu

### 3.2 Department Cards

#### Visual Design
```tsx
interface DepartmentCardProps {
  department: Department;
  status: 'active' | 'inactive' | 'configuring';
  agents: Agent[];
  metrics?: DepartmentMetrics;
  onActivate?: () => void;
  onConfigure?: () => void;
}
```

#### States
- **Inactive**: Grayscale with "Activate" CTA
- **Configuring**: Progress indicators and setup steps
- **Active**: Full color with status indicators
- **Error**: Red accent with troubleshooting actions

#### Content Structure
- Department icon (24x24px)
- Department name and description
- Agent count and status
- Key metrics or recent activity
- Primary action button

### 3.3 Configuration Wizard

#### Progressive Disclosure
- **Step indicators**: Visual progress (1/5, 2/5, etc.)
- **Navigation**: Previous/Next/Skip controls
- **Validation**: Real-time feedback on inputs
- **Preview**: Show outputs before activation

#### Form Elements
- Single-column layout for focus
- Grouped related fields
- Clear labels and help text
- Progressive enhancement with JavaScript

### 3.4 Task Kanban Board

#### Columns
- **Queued**: Tasks waiting to be processed
- **In Progress**: Currently executing tasks
- **Review**: Requiring human approval
- **Completed**: Successfully finished tasks
- **Failed**: Tasks needing attention

#### Task Cards
- Task type icon and name
- Department and agent attribution
- Progress indicators for long-running tasks
- Actions (retry, approve, cancel)

### 3.5 Onboarding Flows

#### Welcome Sequence
- **Slide 1**: Platform introduction
- **Slide 2**: Department overview
- **Slide 3**: Template selection
- **Slide 4**: First setup guidance

#### Interactive Elements
- Progress breadcrumbs
- Contextual tips and help
- Skip options for experienced users
- Success celebrations

---

## 4. Layout Patterns

### 4.1 Grid System

#### Container Sizes
```css
.container {
  max-width: 1280px;
  margin: 0 auto;
  padding: 0 var(--space-6);
}

.container-sm { max-width: 640px; }
.container-md { max-width: 768px; }
.container-lg { max-width: 1024px; }
.container-xl { max-width: 1280px; }
```

#### Grid Columns
- **12-column grid** for complex layouts
- **CSS Grid** for modern browser support
- **Flexbox fallback** for older browsers
- **Responsive breakpoints** at 640px, 768px, 1024px, 1280px

### 4.2 Content Hierarchy

#### Page Structure
```html
<main>
  <header class="page-header">
    <h1>Page Title</h1>
    <div class="page-actions"><!-- CTAs --></div>
  </header>
  
  <section class="content-primary">
    <!-- Main content area -->
  </section>
  
  <aside class="content-sidebar">
    <!-- Contextual information -->
  </aside>
</main>
```

#### Section Spacing
- **Sections**: --space-12 vertical margin
- **Subsections**: --space-8 vertical margin
- **Components**: --space-6 gap between elements
- **Content blocks**: --space-4 internal padding

---

## 5. Interactive States

### 5.1 Button States

#### Primary Button
```css
.btn-primary {
  background: var(--brand-primary);
  border: 1px solid var(--brand-primary);
  color: var(--text-inverse);
  
  &:hover {
    background: color-mix(in srgb, var(--brand-primary) 90%, white);
  }
  
  &:active {
    background: color-mix(in srgb, var(--brand-primary) 80%, black);
  }
  
  &:disabled {
    background: var(--surface-tertiary);
    border-color: var(--surface-border);
    color: var(--text-tertiary);
  }
}
```

#### Focus Management
- **Keyboard focus**: 2px solid brand color outline
- **Skip links**: For screen reader navigation
- **Focus trapping**: In modals and dropdowns

### 5.2 Loading States

#### Skeleton Screens
- Match content structure
- Subtle animation (pulse or shimmer)
- Respect user motion preferences
- Quick appearance/removal

#### Progress Indicators
- **Determinate**: For known duration tasks
- **Indeterminate**: For unknown duration
- **Step progress**: For multi-step processes

---

## 6. Accessibility (A11y)

### 6.1 WCAG 2.1 Compliance

#### Level AA Requirements
- **Color contrast**: Minimum 4.5:1 for normal text, 3:1 for large text
- **Keyboard navigation**: All interactive elements accessible
- **Screen reader support**: Semantic HTML and ARIA labels
- **Focus management**: Visible focus indicators

#### Implementation Checklist
- [ ] Alt text for all informative images
- [ ] Heading hierarchy (h1-h6) properly structured
- [ ] Form labels associated with inputs
- [ ] Error messages clearly announced
- [ ] Live regions for dynamic content updates

### 6.2 Inclusive Design

#### Language Support
- **Primary**: Portuguese (Brazil)
- **Secondary**: English (US)
- **RTL Support**: Planned for future expansion

#### Motor Accessibility
- **Touch targets**: Minimum 44x44px
- **Click areas**: Extend beyond visual boundaries
- **Timing**: No auto-advancing content
- **Motion**: Respect prefers-reduced-motion

---

## 7. Brand Expression

### 7.1 Visual Identity

#### Logo Usage
- **Primary logo**: Full color on dark backgrounds
- **Secondary logo**: Monochrome variants
- **Minimum size**: 24px height for digital
- **Clear space**: 2x logo height on all sides

#### Iconography
- **Style**: Outline-based, 2px stroke weight
- **Sizes**: 16px, 20px, 24px, 32px
- **Library**: Custom icon set + Heroicons as fallback

### 7.2 Tone Through Design

#### Professional Competence
- Clean layouts with generous whitespace
- Consistent alignment and spacing
- High-quality imagery and graphics
- Polished micro-interactions

#### Approachable Innovation
- Subtle animations and transitions
- Progressive disclosure of features
- Contextual help and guidance
- Success celebrations and achievements

---

## 8. Implementation Guidelines

### 8.1 CSS Architecture

#### Methodology
- **CSS-in-JS**: Styled-components or Emotion
- **Design tokens**: Exported from design system
- **Component variants**: Using compound components
- **Responsive design**: Mobile-first approach

#### File Structure
```
styles/
├── tokens/
│   ├── colors.ts
│   ├── typography.ts
│   ├── spacing.ts
│   └── index.ts
├── components/
│   ├── Button.styles.ts
│   ├── Card.styles.ts
│   └── index.ts
└── globals.css
```

### 8.2 Component Development

#### Best Practices
- **Compound components**: For complex UI elements
- **Render props**: For flexible customization
- **TypeScript**: Full type safety
- **Storybook**: Component documentation and testing

#### Performance Considerations
- **Code splitting**: Load components on demand
- **Tree shaking**: Remove unused code
- **Critical CSS**: Inline above-the-fold styles
- **Font optimization**: Preload critical fonts

---

## 9. Quality Assurance

### 9.1 Design Reviews

#### Review Checklist
- [ ] Follows design token system
- [ ] Meets accessibility requirements
- [ ] Responsive across all breakpoints
- [ ] Consistent with brand guidelines
- [ ] Performance optimized

#### Browser Support
- **Modern browsers**: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- **Mobile browsers**: iOS Safari 14+, Chrome Mobile 90+
- **Progressive enhancement**: Core functionality without JavaScript

### 9.2 Testing Strategy

#### Visual Regression Testing
- **Percy** or **Chromatic** for component snapshots
- **Cross-browser testing** with BrowserStack
- **Responsive testing** across device sizes

#### Accessibility Testing
- **Automated**: axe-core, pa11y
- **Manual**: Screen reader testing
- **User testing**: With accessibility consultants

---

## 10. Maintenance and Evolution

### 10.1 Version Control

#### Design Token Updates
- **Semantic versioning** for breaking changes
- **Migration guides** for major updates
- **Deprecation notices** for removed tokens
- **Changelog** documentation

#### Component Library
- **Release notes** for component updates
- **Breaking change notifications**
- **Backward compatibility** considerations

### 10.2 Feedback Loop

#### Usage Analytics
- Component adoption rates
- Performance metrics
- User experience feedback
- Accessibility compliance monitoring

#### Continuous Improvement
- Regular design system audits
- User research integration
- Design trend evaluation
- Technology stack updates

---

## Appendix

### A. Component Specifications

Detailed specifications for all components are maintained in Storybook and include:
- Visual examples
- Code samples
- Accessibility notes
- Usage guidelines
- Do's and don'ts

### B. Brand Assets

All brand assets (logos, icons, imagery) are version-controlled and available through:
- Design system package
- Figma shared library
- Brand asset repository

### C. Tools and Resources

#### Design Tools
- **Figma**: Primary design tool
- **Figma Tokens**: Design token management
- **Figma to Code**: Component code generation

#### Development Tools
- **Storybook**: Component library documentation
- **Chromatic**: Visual testing and review
- **Design Lint**: Automated design system compliance

---

**Document Status**: Ready for development implementation and Figma design system creation.