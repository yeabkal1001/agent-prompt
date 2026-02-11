# Universal Agent System - Adaptive Domain Expertise

## Design Philosophy

This 15-agent system is designed to **automatically adapt** to the project type (web, mobile, e-commerce, i18n, enterprise) and operate as **elite domain specialists** without requiring new agents.

## Constraint

**Jules Free Plan: 15 sessions per day maximum**
- Cannot create additional agents
- Cannot run same agent twice per day
- Must work within these 15 slots

## Solution: Universal Adaptive Agents

Each agent includes **multi-domain expertise** and **project-type detection** to automatically switch between specialist modes.

---

## Agent Role Distribution (15 Agents)

### Coordination & Planning (3 agents)
1. **Agent-1**: Conductor (Start-of-Day) - 08:00
2. **Agent-2**: Product Manager - 09:00
3. **Agent-15**: Conductor (End-of-Day) - 22:00

### Architecture & Design (2 agents)
4. **Agent-3**: Architect (Full-Stack + Mobile + Infrastructure)
5. **Agent-6**: UX Designer (Web + Mobile + Cross-Platform)

### Implementation (2 agents)
6. **Agent-4**: Backend Engineer (API + Database + DevOps + Payments)
7. **Agent-5**: Frontend Engineer (Web + Mobile + PWA + Native)

### Quality & Validation (4 agents)
8. **Agent-7**: QA Engineer (Web + Mobile + E-Commerce + Load Testing)
9. **Agent-8**: Security Engineer (Web + Mobile + PCI-DSS + GDPR)
10. **Agent-9**: Performance Engineer (Web + Mobile + Database + CDN)
11. **Agent-13**: User Tester (Web + Mobile + Accessibility + I18n)

### Specialized Support (4 agents)
12. **Agent-10**: Documentation (API + Mobile Docs + I18n Docs)
13. **Agent-11**: Human Task Manager (DevOps + Compliance + Third-Party)
14. **Agent-12**: Code Reviewer (All Domains + Best Practices)
15. **Agent-14**: System Optimizer (Cross-Domain Patterns)

---

## Project-Type Detection System

### Detection Method

Agents detect project type by analyzing:

```markdown
## Project Type Detection

1. **Check `.jules/pm/product-vision.md`**
   - Look for keywords: "mobile app", "iOS", "Android", "e-commerce", "shopping cart", "payment", "multilingual", "i18n"

2. **Check Directory Structure**
   - `src/ios/` or `src/android/` → Mobile
   - `package.json` with "react-native" or "flutter" → Cross-platform mobile
   - `stripe`, `shopify`, `woocommerce` in dependencies → E-commerce
   - `i18n/`, `locales/`, `translations/` directories → I18n
   - `kubernetes/`, `terraform/` → Enterprise infrastructure

3. **Check Tech Stack**
   - `.jules/tech-stack.md` explicitly states project type

4. **Default**: Assume web application if unclear
```

### Project Type Indicators

```markdown
## Project Type Matrix

| Indicator | Web | Mobile | E-Commerce | I18n | Enterprise |
|-----------|-----|--------|------------|------|------------|
| React/Vue/Angular | ✅ | | | | |
| React Native/Flutter | | ✅ | | | |
| Stripe/PayPal integration | | | ✅ | | |
| i18n libraries, locales/ | | | | ✅ | |
| Kubernetes/Terraform | | | | | ✅ |
| Shopping cart logic | | | ✅ | | |
| App Store references | | ✅ | | | |
| Multi-region deployment | | | | ✅ | ✅ |
```

---

## Domain Coverage Requirements

### Target: 90%+ Coverage for All Domains

| Domain | Current | Target | Strategy |
|--------|---------|--------|----------|
| Simple Web Apps | 90% | 95% | ✅ Already strong |
| B2B SaaS | 70% | 90% | Add compliance, multi-tenancy |
| E-Commerce | 40% | 90% | Add payments, inventory, PCI-DSS |
| Mobile Apps | 10% | 90% | Add native dev, app stores, mobile testing |
| Enterprise Systems | 60% | 90% | Add DevOps, observability, DR |
| I18n Products | 30% | 90% | Add localization, RTL, regional compliance |

---

## Universal Agent Enhancements

### Each Agent Must Include:

1. **Project Type Detection Section**
   ```markdown
   ## PROJECT TYPE DETECTION
   
   Before starting work, detect project type:
   - Read .jules/pm/product-vision.md
   - Check directory structure
   - Check .jules/tech-stack.md
   - Determine: WEB | MOBILE | ECOMMERCE | I18N | ENTERPRISE
   ```

2. **Multi-Domain Expertise Sections**
   ```markdown
   ## DOMAIN-SPECIFIC RESPONSIBILITIES
   
   ### IF WEB APPLICATION:
   [Web-specific tasks and best practices]
   
   ### IF MOBILE APPLICATION:
   [Mobile-specific tasks and best practices]
   
   ### IF E-COMMERCE:
   [E-commerce-specific tasks and best practices]
   
   ### IF I18N/GLOBAL:
   [I18n-specific tasks and best practices]
   
   ### IF ENTERPRISE:
   [Enterprise-specific tasks and best practices]
   ```

3. **Human Interaction Protocol**
   ```markdown
   ## HUMAN INTERACTION
   
   ### Autonomy Mode (Default)
   - Work independently
   - Make decisions based on best practices
   - Create tasks for human only when blocked (third-party setup, credentials)
   
   ### Human Input Recognition
   - Check .jules/human-feedback/ for suggestions
   - Check PR comments for feature requests
   - Check .jules/tickets/ for human-created tasks
   - Integrate human suggestions naturally (not as dependencies)
   
   ### Escalation Policy
   - ONLY escalate to human for:
     1. Third-party account setup (Stripe, App Store, etc.)
     2. Credentials/secrets provisioning
     3. Legal/compliance decisions
     4. Critical production decisions
   ```

---

## Specific Domain Expertise Distribution

### Agent-4 (Backend) Additions

**Must handle:**
- **E-Commerce**: Payment processing (Stripe, PayPal), order management, inventory sync, webhook handling
- **Mobile Backend**: Push notifications, deep linking, app versioning APIs
- **Enterprise**: Multi-tenancy, SSO, audit logs, data residency

### Agent-5 (Frontend) Additions

**Must handle:**
- **Web**: React/Vue/Angular, SSR, PWA, responsive design
- **Mobile**: React Native/Flutter, native APIs, camera, GPS, push notifications
- **E-Commerce**: Shopping cart, checkout flows, payment UI
- **I18n**: RTL support, locale switching, currency formatting

### Agent-6 (UX) Additions

**Must handle:**
- **Mobile**: Touch gestures, mobile patterns, native UI guidelines (iOS HIG, Material Design)
- **E-Commerce**: Checkout optimization, trust signals, cart abandonment prevention
- **I18n**: Cultural considerations, icon interpretation, date/time formats

### Agent-7 (QA) Additions

**Must handle:**
- **Mobile**: Device matrix testing, OS version compatibility, app store review criteria
- **E-Commerce**: Payment flow testing, tax calculation, inventory edge cases
- **I18n**: Locale-specific testing, character encoding, date/currency validation

### Agent-8 (Security) Additions

**Must handle:**
- **Mobile**: Certificate pinning, jailbreak detection, secure storage
- **E-Commerce**: PCI-DSS compliance, fraud prevention, secure checkout
- **I18n**: GDPR, data residency, region-specific regulations

### Agent-11 (Human Tasks) Additions

**Must handle:**
- **Third-Party Setup**: App Store account, Google Play, payment gateways, CDN
- **DevOps**: Cloud account setup, domain registration, SSL certificates
- **Compliance**: Legal review requests, privacy policy, terms of service

---

## Autonomy vs. Human Input

### Default Operating Mode: **Autonomous**

Agents operate independently and make decisions based on:
- Best practices for detected project type
- Industry standards
- Security requirements
- Performance requirements

### Human Input Integration

**Sources of Human Input:**
1. `.jules/human-feedback/[DATE]-suggestions.md` - Human feature ideas
2. PR comments - Human code review or feature requests
3. `.jules/tickets/HUMAN-CREATED-*.md` - Explicit human tasks

**Integration Approach:**
```markdown
When human input detected:
1. READ human suggestion/feedback
2. EVALUATE against current architecture and goals
3. IF VALUABLE: Integrate into current work naturally
4. IF UNCLEAR: Create clarifying question in human-tasks
5. IF OUT OF SCOPE: Document in future-features.md

NEVER:
- Block work waiting for human
- Treat human input as hard requirement (unless explicitly marked REQUIRED)
- Ignore human input (always acknowledge and consider)
```

---

## Coverage Improvement Strategy

### Achieving 90%+ Coverage Without New Agents

**Web Applications (90% → 95%)**
- ✅ Already strong
- Add: Advanced PWA, Web Workers, Service Workers

**B2B SaaS (70% → 90%)**
- Add: Multi-tenancy patterns, SSO integration, role-based access
- Add: Usage analytics, billing integration, subscription management

**E-Commerce (40% → 90%)**
- Add: Payment gateway integration (Stripe, PayPal, Square)
- Add: Inventory management, order state machines
- Add: Tax calculation, shipping integration
- Add: PCI-DSS compliance validation
- Add: Fraud detection basics

**Mobile Apps (10% → 90%)**
- Add: Native development (iOS/Android fundamentals)
- Add: Cross-platform (React Native, Flutter) 
- Add: App Store submission process
- Add: Mobile-specific testing (devices, OS versions)
- Add: Push notifications, deep linking, camera, GPS

**Enterprise Systems (60% → 90%)**
- Add: Infrastructure as Code (Terraform basics)
- Add: Container orchestration (Kubernetes basics)
- Add: CI/CD pipeline setup
- Add: Observability (logging, metrics, tracing)
- Add: Disaster recovery procedures

**I18n Products (30% → 90%)**
- Add: Multi-language setup (i18n libraries)
- Add: Locale-specific formatting (date, time, currency)
- Add: RTL language support
- Add: Translation workflow management
- Add: Regional compliance (GDPR per country)

---

## Implementation Checklist

### Phase 1: Add Project Type Detection (All Agents)
- [ ] Add detection logic to each agent's startup
- [ ] Create detection utility section
- [ ] Test with different project types

### Phase 2: Add Multi-Domain Expertise (Per Agent)
- [ ] Agent-3 (Architect): All architectures
- [ ] Agent-4 (Backend): API + Mobile Backend + E-Commerce + DevOps
- [ ] Agent-5 (Frontend): Web + Mobile + PWA
- [ ] Agent-6 (UX): Web + Mobile + E-Commerce patterns
- [ ] Agent-7 (QA): Web + Mobile + E-Commerce testing
- [ ] Agent-8 (Security): All security domains
- [ ] Agent-9 (Performance): All platforms
- [ ] Agent-10 (Docs): Multi-platform docs
- [ ] Agent-11 (Human Tasks): All third-party needs
- [ ] Agent-13 (User Tester): All platforms

### Phase 3: Add Human Interaction Protocol (All Agents)
- [ ] Autonomy-first approach
- [ ] Human input recognition
- [ ] Natural integration of suggestions
- [ ] Minimal blocking on human

### Phase 4: Split Conductor (Agent-1 and Agent-15)
- [ ] Agent-1: SOD responsibilities only
- [ ] Agent-15: EOD responsibilities only
- [ ] Ensure proper handoff between them

---

## Success Metrics

**Coverage Targets:**
- Simple Web Apps: 95%+ ✅
- B2B SaaS: 90%+ (from 70%)
- E-Commerce: 90%+ (from 40%)
- Mobile Apps: 90%+ (from 10%)
- Enterprise Systems: 90%+ (from 60%)
- I18n Products: 90%+ (from 30%)

**Autonomy Metrics:**
- <10% of work requires human intervention
- >90% of decisions made by agents
- Human input integrated within 1 cycle (not blocking)

**Quality Metrics:**
- Same high standards regardless of project type
- Automatic best practices for detected domain
- Zero manual domain switching required
