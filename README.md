<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=180&section=header&text=Mahmoud%20Bakr&fontSize=52&fontColor=fff&animation=twinkling&fontAlignY=38&desc=Senior%20Ruby%20on%20Rails%20Backend%20Engineer&descAlignY=60&descSize=18" />
</p>

<p align="center">
  <a href="https://www.linkedin.com/in/m-bakr/">
    <img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/>
  </a>
  <a href="https://bakrblog.netlify.app">
    <img src="https://img.shields.io/badge/Blog-FF5722?style=for-the-badge&logo=rss&logoColor=white"/>
  </a>
  <a href="https://medium.com/@mbakr6821">
    <img src="https://img.shields.io/badge/Medium-12100E?style=for-the-badge&logo=medium&logoColor=white"/>
  </a>
  <a href="https://drive.google.com/file/d/1zcR0OUJ5Fn4afdjLiAYvhEKAbPHA8LSF/view?usp=sharing">
    <img src="https://img.shields.io/badge/Resume-4285F4?style=for-the-badge&logo=google-drive&logoColor=white"/>
  </a>
  <a href="mailto:mbakr6821@gmail.com">
    <img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/>
  </a>
</p>

---

Senior backend engineer with **5+ years** building production-grade Ruby on Rails APIs. Currently at [Tadarab](https://tadarab.com) owning **payment architecture**, **subscription systems**, and **B2B SaaS infrastructure** for a growing EdTech platform in Kuwait.

**What I specialize in:**
- 🤖 AI-native engineering — agentic coding workflows, LLM tool/MCP orchestration, and multi-agent system design integrated into daily practice to accelerate delivery and reduce cognitive overhead
- 🏗️ Designing backend systems for scale and long-term maintainability — explicit service layers, state machines, partitioned schemas — built to stay comprehensible as teams and traffic scale
- ⚡ API performance engineering — query optimization, caching, async job architecture — with systems designed to scale and correctness guarantees that hold under load (**~95% reduction in failed transactions**)
- 🧠 Architectural decision-making grounded in actual NFRs — strong consistency where money is involved, eventual consistency where fan-out allows it, trade-offs made explicit, not discovered in production
- 💳 Financial infrastructure correctness — multi-gateway integrations, double-entry ledgers, idempotency guarantees, HMAC-signed webhook delivery — shipped across 7 payment providers in production
- 📦 Subscription and growth systems — referral programs, upsell flows — **~55% upsell boost and ~40% improvement in subscriber retention** in production
- 🔧 Automation over manual toil — converting high-frequency repetitive processes into parameterized async services and background pipelines, cutting resolution time by up to **90%** and freeing teams to focus on what can't be automated
- 🧪 Quality before shipping — comprehensive unit and integration suites, cross-environment validation (staging → production), and agentic testing with tools like Playwright MCP to catch regressions before they reach users

Former **Senior Technical Mentor at Microverse** — reviewed code and mentored **100+ developers** worldwide across Ruby on Rails and JavaScript.

---

## Featured Project — Paygate

> A production-grade payment gateway — built as a complete, multi-repo system

Paygate is a full-stack payment infrastructure portfolio: a REST API, merchant dashboard, developer documentation site, and a drop-in JavaScript SDK (`@paygater/js`) published on npm. Engineered with financial-grade patterns: **double-entry ledger**, **idempotency**, **HMAC-signed webhook delivery**, and a **charge state machine**.

<table>
  <thead>
    <tr>
      <th></th>
      <th><a href="https://github.com/MahmoudBakr23/paygate-api">paygate-api</a></th>
      <th><a href="https://github.com/MahmoudBakr23/paygate-dashboard">paygate-dashboard</a></th>
      <th><a href="https://github.com/MahmoudBakr23/paygate-docs">paygate-docs</a></th>
      <th><a href="https://github.com/MahmoudBakr23/paygate-js">paygate-js</a></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>Role</b></td>
      <td>Core API &amp; billing engine</td>
      <td>Merchant UI</td>
      <td>Developer docs</td>
      <td>Client-side JS SDK</td>
    </tr>
    <tr>
      <td><b>Stack</b></td>
      <td>Rails 8.1 · PostgreSQL · Sidekiq</td>
      <td>Next.js 15 · TypeScript · shadcn/ui</td>
      <td>Next.js 15 · Fumadocs · MDX</td>
      <td>TypeScript · Stripe.js</td>
    </tr>
    <tr>
      <td><b>Deployed</b></td>
      <td>Fly.io</td>
      <td>Vercel</td>
      <td>Vercel</td>
      <td>npm · jsDelivr CDN</td>
    </tr>
    <tr>
      <td><b>Live</b></td>
      <td><a href="https://paygate-api.fly.dev/api-docs">OpenAPI Spec ↗</a></td>
      <td><a href="https://paygate-dashboard.vercel.app">Dashboard ↗</a></td>
      <td><a href="https://paygate-docs.vercel.app">Docs ↗</a></td>
      <td><a href="https://www.npmjs.com/package/@paygater/js">npm ↗</a></td>
    </tr>
  </tbody>
</table>

### Architecture

```
POST /v1/charges
       │
       ▼
AuthenticateRequest       ← API key (sk_test_xxx) or JWT session
       │
       ▼
IdempotencyService        ← Redis 24h lock per Idempotency-Key header
       │
       ▼
PaymentRouterService      ← Strategy pattern: routes by payment_method + environment
       │
       ├── StripeAdapter       → Visa / Mastercard / Apple Pay
       └── CheckoutAdapter     → Mada
       │
       ▼
ChargeService             ← State machine · double-entry ledger · audit log
       │
       ▼
WebhookDispatcherService  → Sidekiq → HMAC-SHA256 signed · exponential retry (5 attempts)
```

**Charge lifecycle:** `PENDING → AUTHORIZED → CAPTURED → REFUNDED` / `FAILED` / `VOIDED`

**Key design rules:**
- Business logic lives in `app/services/` — controllers call one service and render
- Financial data is never cached — always read live from the database
- Every financial state change writes a double-entry `LedgerEntry`
- Every merchant action writes an immutable `AuditLog`
- No PAN, CVV, or raw card data ever touches the server — client-side tokenization only

### API Stack

| Layer | Technology |
|---|---|
| Framework | Rails 8.1 (API mode) |
| Database | PostgreSQL — `charges` table range-partitioned by `created_at` |
| Cache / Queues | Redis + Sidekiq 8.1 |
| Auth | bcrypt API keys + JWT sessions + Rack::Attack rate limiting |
| Payment adapters | Stripe 13 (Visa/MC/Apple Pay) · Checkout.com REST (Mada) |
| Serialization | Blueprinter |
| Logging | Lograge — structured JSON, no PAN |
| API spec | rswag — OpenAPI 3.0.1, 260 request examples |
| Deployment | Fly.io (web + worker machines) |

---

## Tech Stack

**Backend**

![Ruby](https://img.shields.io/badge/Ruby-CC342D?style=flat-square&logo=ruby&logoColor=white)
![Rails](https://img.shields.io/badge/Ruby%20on%20Rails-CC0000?style=flat-square&logo=ruby-on-rails&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=flat-square&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![Sidekiq](https://img.shields.io/badge/Sidekiq-B1003E?style=flat-square&logoColor=white)
![GoodJob](https://img.shields.io/badge/GoodJob-3B82F6?style=flat-square&logoColor=white)
![Action Cable](https://img.shields.io/badge/Action%20Cable-CC0000?style=flat-square&logo=ruby-on-rails&logoColor=white)
![Pundit](https://img.shields.io/badge/Pundit-4A4A4A?style=flat-square&logoColor=white)
![Blueprinter](https://img.shields.io/badge/Blueprinter-6B7280?style=flat-square&logoColor=white)
![Lograge](https://img.shields.io/badge/Lograge-374151?style=flat-square&logoColor=white)

**Auth & Security**

![Devise](https://img.shields.io/badge/Devise-CC0000?style=flat-square&logo=ruby-on-rails&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?style=flat-square&logo=json-web-tokens&logoColor=white)
![OAuth 2.0](https://img.shields.io/badge/OAuth%202.0-EB5424?style=flat-square&logo=auth0&logoColor=white)
![SAML 2.0](https://img.shields.io/badge/SAML%202.0-F4B400?style=flat-square&logoColor=white)
![bcrypt](https://img.shields.io/badge/bcrypt-4A4A4A?style=flat-square&logoColor=white)
![Rack::Attack](https://img.shields.io/badge/Rack::Attack-CC342D?style=flat-square&logoColor=white)
![HMAC](https://img.shields.io/badge/HMAC--SHA256-374151?style=flat-square&logoColor=white)

**Payment & Integrations**

![Stripe](https://img.shields.io/badge/Stripe-635BFF?style=flat-square&logo=stripe&logoColor=white)
![Checkout.com](https://img.shields.io/badge/Checkout.com-00C2FF?style=flat-square&logoColor=white)
![HyperPay](https://img.shields.io/badge/HyperPay-003087?style=flat-square&logoColor=white)
![PayPal](https://img.shields.io/badge/PayPal-003087?style=flat-square&logo=paypal&logoColor=white)
![Tap](https://img.shields.io/badge/Tap%20Payments-1A1A2E?style=flat-square&logoColor=white)
![Tamara](https://img.shields.io/badge/Tamara-00A651?style=flat-square&logoColor=white)
![Apple Pay](https://img.shields.io/badge/Apple%20Pay-000000?style=flat-square&logo=apple-pay&logoColor=white)
![Deema](https://img.shields.io/badge/Deema-6C3483?style=flat-square&logoColor=white)

**Cloud & DevOps**

![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=amazon-aws&logoColor=white)
![ECS](https://img.shields.io/badge/ECS-FF9900?style=flat-square&logo=amazon-aws&logoColor=white)
![S3](https://img.shields.io/badge/S3-569A31?style=flat-square&logo=amazon-s3&logoColor=white)
![RDS](https://img.shields.io/badge/RDS-527FFF?style=flat-square&logo=amazon-rds&logoColor=white)
![SQS](https://img.shields.io/badge/SQS-FF4F8B?style=flat-square&logo=amazon-sqs&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=github-actions&logoColor=white)
![Kamal](https://img.shields.io/badge/Kamal-CC0000?style=flat-square&logo=ruby-on-rails&logoColor=white)
![Fly.io](https://img.shields.io/badge/Fly.io-7C3AED?style=flat-square&logo=flydotio&logoColor=white)
![Vercel](https://img.shields.io/badge/Vercel-000000?style=flat-square&logo=vercel&logoColor=white)

**Monitoring & Observability**

![New Relic](https://img.shields.io/badge/New_Relic-008C99?style=flat-square&logo=new-relic&logoColor=white)
![Sentry](https://img.shields.io/badge/Sentry-362D59?style=flat-square&logo=sentry&logoColor=white)
![Brakeman](https://img.shields.io/badge/Brakeman-CC342D?style=flat-square&logoColor=white)

**Testing & Quality**

![RSpec](https://img.shields.io/badge/RSpec-CC342D?style=flat-square&logo=ruby&logoColor=white)
![FactoryBot](https://img.shields.io/badge/FactoryBot-4A4A4A?style=flat-square&logoColor=white)
![Capybara](https://img.shields.io/badge/Capybara-6B7280?style=flat-square&logoColor=white)
![RuboCop](https://img.shields.io/badge/RuboCop-000000?style=flat-square&logoColor=white)
![OpenAPI](https://img.shields.io/badge/OpenAPI-6BA539?style=flat-square&logo=openapiinitiative&logoColor=white)

**Frontend (supporting)**

![React](https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=next.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=flat-square&logo=typescript&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/TailwindCSS-38B2AC?style=flat-square&logo=tailwind-css&logoColor=white)
![shadcn/ui](https://img.shields.io/badge/shadcn%2Fui-000000?style=flat-square&logoColor=white)
![WebRTC](https://img.shields.io/badge/WebRTC-333333?style=flat-square&logo=webrtc&logoColor=white)

---

## Writing

Technical deep-dives on backend engineering, distributed systems, and production architecture.

| Article | Topic |
|---|---|
| [**Idempotency in Distributed Systems: 5 Patterns Every Backend Engineer Should Know**](https://bakrblog.netlify.app/posts/idempotency-patterns/) | Distributed systems · Payments |
| [**Webhooks: How External Systems Talk Back to Yours — Securely**](https://bakrblog.netlify.app/posts/webhooks-explained/) | Webhooks · HMAC · Event-driven |
| [**The Complete Software Architecture Walkthrough**](https://bakrblog.netlify.app/posts/complete-software-architecture-walkthrough/) | System design · Rails architecture |
| [**When the Cloud Burns: Why RTO and RPO Aren't Just Buzzwords**](https://bakrblog.netlify.app/posts/when-the-cloud-burns-rto-and-rpo/) | Disaster recovery · Infrastructure |
| [**Boosting API Performance with Caching in Rails**](https://bakrblog.netlify.app) | Rails · Performance · Caching |

---

## GitHub Stats

<p align="center">
  <img src="https://streak-stats.demolab.com?user=MahmoudBakr23&theme=github-dark&hide_border=true&date_format=j%20M%5B%20Y%5D" />
</p>

<p align="center">
  <img src="https://komarev.com/ghpvc/?username=MahmoudBakr23&label=Profile+views&color=0e75b6&style=flat" />
</p>

---

<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=100&section=footer"/>
</p>

<p align="center"><i>Building things that process real money, serve real users, and stay up.</i></p>
