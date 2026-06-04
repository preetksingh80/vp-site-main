# Vishvaas Properties Site - Implementation Plan

## 1. Project Goals
- Launch a production-ready marketing site for Vishvaas Properties.
- Present two business lines clearly:
  - Core property operation: buy, refurbish, refinance, let/sell.
  - PropTech roadmap: VP Manager AI for landlords/agencies/tenants.
- Deploy on Azure Static Web Apps and map vishvaas.co.uk domain.

## 2. What Is Already Done
- Static site scaffold created in index.html.
- Branding aligned with KB sample color direction and logo usage.
- Required menu sections included:
  - Home
  - Tenants
  - Landlords
  - Investors
  - Building services
  - VP Manager AI
- Static VP Manager AI demo implemented with:
  - Prompt input and send button
  - 5-property table with UK dummy addresses
  - Realistic AI response for two selected properties
- Companies House summary details added (company number and incorporation date).

## 3. Work Plan (Execution Phases)

### Phase A - Content Refinement
- Review all copy for tone, grammar, and compliance language.
- Confirm legal/disclaimer text for:
  - investment language
  - service availability wording
  - demo-only AI functionality
- Add final contact email/phone/office details.

### Phase B - UX and Design Polish
- Add responsive mobile menu interaction (if we keep pure static behavior).
- Add subtle animations for section reveal and CTA focus.
- Replace temporary stock images with approved project photos.
- Validate accessibility:
  - color contrast
  - heading structure
  - form labels
  - keyboard navigation

### Phase C - Contact Flows
- Option 1: keep forms as static UI with mailto fallback.
- Option 2 (recommended): connect forms to an Azure Function or Formspree endpoint.
- Add anti-spam protection (honeypot or captcha if backend exists).

### Phase D - VP Manager Integration Hooks
- Add CTA link to VP Manager V2 repo or live environment when available.
- Define API contract for future live demo widget (chat + task board).
- Add feature flags for agency user tiers (up to 5 users for agency plan).

### Phase E - Deployment and Domain
- Configure Azure Static Web App deployment from GitHub repository.
- Add production and preview branches strategy.
- Map vishvaas.co.uk and www.vishvaas.co.uk to SWA.
- Execute DNS validation and cutover checklist (see deploy/domain-mapping-guide.md).

## 4. Delivery Backlog (Prioritized)
1. Add final verified company profile block with Companies House link.
2. Add favicon and social preview metadata (Open Graph/Twitter tags).
3. Add privacy policy and cookie notice pages.
4. Wire contact forms to backend endpoint.
5. Add analytics (GA4 or privacy-friendly equivalent).
6. Run Lighthouse and accessibility pass.
7. Deploy and map custom domain.

## 5. Repo and Git Workflow
- Repository name target: vp-site-main.
- Branching:
  - main: production
  - develop: staging/integration
  - feature/*: isolated implementation tasks
- Commit rhythm:
  - small commits by feature
  - every completed checklist item committed
  - push after each stable change set

## 6. Done Criteria for V1 Launch
- Mobile and desktop rendering validated.
- Contact forms submit successfully to chosen endpoint.
- Domain points correctly to Azure Static Web App.
- SSL certificate active and no browser warnings.
- Core sections signed off by business owner.
- Performance and accessibility at acceptable threshold.

## 7. Open Items for You to Confirm
- Official public contact email and phone number.
- Preferred form backend (Azure Function vs third-party forms).
- Final project photos to replace temporary images.
- Whether www should redirect to apex domain or vice versa.
- Exact legal pages required at launch (privacy/terms/cookies).
