# Project Type Detection System

## Overview

While the current agent system focuses on Next.js websites, it should still detect and adapt to different project types for flexibility.

## Detection Method

When Agent-2 (Product Strategist) runs, it should detect project type from `.jules/website-idea.md`:

```bash
# Read the website idea
IDEA_CONTENT=$(cat .jules/website-idea.md | tr '[:upper:]' '[:lower:]')

# Score each type
WEBSITE_SCORE=$(echo "$IDEA_CONTENT" | grep -oiE "website|web app|landing page|portfolio|blog|marketing site" | wc -l)
SAAS_SCORE=$(echo "$IDEA_CONTENT" | grep -oiE "saas|platform|dashboard|app|tool|service" | wc -l)
ECOMMERCE_SCORE=$(echo "$IDEA_CONTENT" | grep -oiE "ecommerce|shop|store|product|cart|checkout|payment" | wc -l)
BLOG_SCORE=$(echo "$IDEA_CONTENT" | grep -oiE "blog|article|content|news|magazine" | wc -l)

# Determine type
if [ $ECOMMERCE_SCORE -gt 1 ]; then
    PROJECT_TYPE="ECOMMERCE"
elif [ $SAAS_SCORE -gt 1 ]; then
    PROJECT_TYPE="SAAS"
elif [ $BLOG_SCORE -gt 1 ]; then
    PROJECT_TYPE="BLOG"
else
    PROJECT_TYPE="WEBSITE"
fi

# Document
echo "Detected Project Type: $PROJECT_TYPE" >> .jules/strategy/project-type.md
```

## Project Types

### 1. WEBSITE (Default)
**Examples:** Portfolio, landing page, marketing site, brochure site
**Focus:** Content, design, conversion
**Key Features:**
- Static pages
- Contact forms
- Content sections
- SEO optimization
- Responsive design

**Agent Adaptations:**
- **Backend:** Minimal, mostly static
- **Frontend:** Heavy focus on design/animations
- **Database:** Optional, simple contact form storage
- **Priority:** Visual design > functionality

### 2. SAAS
**Examples:** Project management tool, analytics dashboard, productivity app
**Focus:** Functionality, user experience, scalability
**Key Features:**
- User authentication
- Dashboard with data
- CRUD operations
- Real-time updates (optional)
- Subscription billing

**Agent Adaptations:**
- **Backend:** Complex API, authentication, business logic
- **Frontend:** Interactive UI, state management
- **Database:** Complex schema, user data
- **Priority:** Functionality > visual polish

### 3. ECOMMERCE
**Examples:** Online store, product catalog, digital goods shop
**Focus:** Conversion, payments, inventory
**Key Features:**
- Product catalog
- Shopping cart
- Checkout flow
- Payment processing
- Order management

**Agent Adaptations:**
- **Backend:** Product API, inventory, payments
- **Frontend:** Product pages, cart, checkout
- **Database:** Products, orders, customers
- **Priority:** Trust/safety > design

### 4. BLOG/CONTENT
**Examples:** Personal blog, news site, publication
**Focus:** Content, readability, SEO
**Key Features:**
- Article pages
- Categories/tags
- Search
- RSS feeds
- Comment system (optional)

**Agent Adaptations:**
- **Backend:** Content API, search
- **Frontend:** Typography, reading experience
- **Database:** Posts, authors, tags
- **Priority:** Content > design

## Type-Specific Guidelines

### For WEBSITE Projects
```markdown
# Website-Specific Guidelines

## Backend (Agent-6)
- Minimal API
- Focus: Contact forms, static data
- Optional: CMS integration (Contentful, Sanity)

## Frontend (Agent-7)
- Heavy animations
- Scroll-triggered effects
- Parallax sections
- Max visual impact

## Animations (Agent-10)
- Hero entrance animations
- Scroll reveal on all sections
- Micro-interactions on buttons
- Smooth page transitions
```

### For SAAS Projects
```markdown
# SaaS-Specific Guidelines

## Backend (Agent-6)
- RESTful API
- Authentication (JWT or sessions)
- CRUD endpoints
- Input validation
- Error handling

## Frontend (Agent-7)
- Dashboard layout
- Data tables
- Form-heavy UI
- Loading states
- Error boundaries

## Database (Agent-8)
- User table
- Core entity tables
- Relationships
- Indexes on query fields
```

### For ECOMMERCE Projects
```markdown
# Ecommerce-Specific Guidelines

## Security (All Agents)
- PCI compliance awareness
- No storing credit cards
- HTTPS everywhere
- Input sanitization

## Backend (Agent-6)
- Product API
- Cart/session management
- Stripe/PayPal integration
- Order workflow

## Frontend (Agent-7)
- Product pages with variants
- Shopping cart UI
- Checkout flow
- Trust signals (reviews, security badges)
```

## Documentation

Agent-2 should create `.jules/strategy/project-type.md`:

```markdown
# Project Type: [TYPE]

## Detected Keywords
- [keyword 1]
- [keyword 2]
- [keyword 3]

## Type Characteristics
- **Primary Focus:** [What matters most]
- **Key Features:** [Must-haves]
- **Technical Complexity:** [Simple/Moderate/Complex]

## Agent Adaptations

### Backend
[Specific backend requirements]

### Frontend  
[Specific frontend requirements]

### Database
[Specific schema requirements]

## Success Metrics
- [ ] Metric 1
- [ ] Metric 2
```

## Flexibility

Even though the system defaults to WEBSITE, having this detection:
1. **Validates** the project is within scope
2. **Warns** if project is too complex (e.g., full social network)
3. **Adapts** agent focus appropriately
4. **Documents** project type for future reference

## Fallback

If project type is unclear or too complex:
1. Default to WEBSITE
2. Document uncertainty in `.jules/strategy/project-type.md`
3. Flag for human review if needed
