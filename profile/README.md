<p align="center">
  <img src="https://raw.githubusercontent.com/business-brain-24/.github/main/profile/assets/banner.svg" alt="Business Brain 24, the Betopia ERP" width="100%">
</p>

# Business Brain 24

**The Betopia ERP for manufacturing and distribution groups.** One connected system from the sales representative's phone to the factory floor to the management screen: demand, dispatch, delivery, cash, manufacturing, procurement, retail and reporting, with an AI assistant that answers from live data and a help portal that speaks the team's language.

Built, run and supported by [Betopia Limited](https://betopialimited.com), Dhaka. First group deployment: Akboria Limited, Bogura (six divisions, since 1911), live since October 2026.

<p>
  <img alt="Field app" src="https://img.shields.io/badge/field%20app-Android%20%7C%20Bangla%20%7C%20offline-0f1a33?style=flat-square">
  <img alt="API" src="https://img.shields.io/badge/REST%20API-v1%20%7C%20bearer%20tokens-2a78b3?style=flat-square">
  <img alt="AI" src="https://img.shields.io/badge/AI%20assistant-67%20tools%20%7C%20confirm%20cards-c07a12?style=flat-square">
  <img alt="Design records" src="https://img.shields.io/badge/design%20records-34%20RFCs%20%7C%2056%20ADRs-23907a?style=flat-square">
</p>

---

## What it does

| Area | In one line |
|---|---|
| **Field sales** | Beat plans, GPS check-in, shop registration, orders by SKU with suggested quantities, deliveries with returns and damage, cash collection against open invoices with carry limits, targets and achievement. Works offline and syncs when the network returns. |
| **Demand to delivery chain** | Dealer and SR demands, manager approval with edits, credit exceptions, consolidated orders, challans with FEFO picking, delivery man acceptance and hand-over, dealer receipt with shortage credit, cash hand-over and day-end settlement. Every step has an audit trail. |
| **Retail and POS** | Showroom and franchise outlet counter app with business days, shifts, refunds, daily stock, remittance, commission and settlement, integrated with stock, demand and accounts. |
| **Manufacturing and QC** | Batch plans and bills of material from recipes, multi-factory work orders, WIP dashboards, QC gates, pack and carton conversion, manufacturing and expiry stamping. |
| **Procurement** | Purchase requisitions, RFQs, tenders and vendor performance with tiered approvals and a vendor portal. |
| **Management and reports** | Role dashboards, 60+ operational reports, daily Z-report, a live map of the whole group (orders, deliveries, cash and the field team as they move). |
| **AI assistant** | "Akboria AI, powered by Beto AI": ask in Bangla or English, get answers from the database with stated totals, how-to help with the page to open, and actions prepared on a confirm card. Nothing changes without a human yes. |
| **Help and learning** | [Sohay](https://sohay.betopia.dev), the Bangla help portal: 84 task guides, 93 app and 240 ERP screen maps, 11 role courses with certificates, glossary, fixes and audio for people who would rather listen than read. |

## How it is built

Business Brain 24 is a modular platform. The ERP core carries the business records and accounting; everything a field team, a counter, a manager or a machine touches goes through contracts that do not depend on the core's internals.

```
 phones and counters          web and wall                 machines and partners
 ┌──────────────────┐   ┌─────────────────────────┐   ┌───────────────────────────┐
 │ Field Sales app  │   │ ERP web console          │   │ MCP server (AI tools)     │
 │ Retail POS app   │   │ Management Console       │   │ Keel (operator plane)     │
 │ Login by QR      │   │ World (live map, TV)     │   │ Webhooks, SSE, queues     │
 └────────┬─────────┘   └────────────┬────────────┘   └──────────────┬────────────┘
          │  REST v1, bearer tokens   │                               │
          └──────────────┬───────────┴───────────────┬───────────────┘
                         ▼                           ▼
                 ┌───────────────┐          ┌──────────────────────┐
                 │  Gateway      │  events  │  Bridge              │
                 │  one edge,    │◄────────►│  CIR contracts,      │
                 │  scopes, T0-T3│          │  cells, plugins,     │
                 └───────┬───────┘          │  realtime hub        │
                         ▼                  └──────────┬───────────┘
                 ┌───────────────┐                     │
                 │  ERP core     │◄────────────────────┘
                 │  58 modules   │  adapter + transactional outbox
                 │  205 models   │
                 └───────┬───────┘
                         ▼
                 PostgreSQL 16 (TLS, per-role access), read replicas for AI and reporting
```

**Principles that hold across every part**

- **Figures come from the database.** The assistant and the dashboards quote stated totals; no model is allowed to add up rows.
- **Four tiers of action.** T0 read, T1 draft, T2 decide or change (always behind a confirm card), T3 irreversible (never offered to an assistant). Per role, per user.
- **Role based access.** Menus, records and API scope follow a role catalogue (SR, delivery man, dealer, sales managers, store, factory, procurement, finance, HR, management, administrator). Changes are logged with the person who made them.
- **Offline first in the field.** The app keeps drafts, routes and lists on the device and syncs with idempotent calls, so a dead network never loses an order.
- **Contracts, not field names.** New clients and plugins speak the Common Internal Representation (JSON Schema with generated TypeScript, Python and Dart types), so the core can change underneath.
- **Everything has a design record.** Large features start as an RFC, decisions as an ADR, work items carry the RFC number through issues, branches and commits.

## The stack

| Layer | Technology |
|---|---|
| ERP core and modules | Python 3, PostgreSQL 16, server-rendered web console with a component UI |
| Field Sales and Retail POS apps | Flutter and Dart (Android first; web build for the counter), local SQLite, secure storage for tokens |
| Bridge, Gateway, Console, Keel | TypeScript on Cloudflare Workers and Pages, D1, R2, queues; Python host agent |
| Sohay help portal | Astro static site, Bangla content, text to speech audio, full-text search with Banglish aliases |
| World live map | Pixi.js canvas over OpenStreetMap and geoBoundaries data |
| AI assistant | MCP server inside the ERP, tool tiers and previews, served to the Beto AI assistant in the web console and the app |
| Operations | Docker images per release tag, GitHub Actions gates (test suite, backup, upgrade, health), Cloudflare in front of every public surface, TLS between every host |
| Quality | 500+ server tests in the release gate, Playwright web suite, emulator journeys for the app, golden tests for every assistant tool |

## Repositories

| Repository | What is in it |
|---|---|
| [project-erp](https://github.com/business-brain-24/project-erp) | The operations and design repo: RFCs and ADRs, release logs, deployment scripts, Bridge, Keel, Sohay, World and the evidence from every test run. Code repos are linked as submodules. |
| [akboria_dev](https://github.com/business-brain-24/akboria_dev) | The ERP modules: field sales, demand chain, manufacturing, procurement, dashboards, REST API v1, MCP server, access control, audit. |
| [akboria_mobile_app](https://github.com/business-brain-24/akboria_mobile_app) | The Field Sales app (mirror of the GitLab origin): SR, ASM, dealer, delivery man, warehouse and outlet roles. |
| [retail-sale](https://github.com/business-brain-24/retail-sale) | Retail: the counter app and the retail service as a standalone product. |

Repositories are private while the platform is in its first deployments. Developers and partners who work with us get access through Betopia; write to the address below.

## For developers

**Branches and releases.** `release` is the line that ships; production deploys are annotated tags on it (`vYYYY.MM.DD.N` for the ERP, `vX.Y.Z+N` for the app). Developer work enters `release` by pull request. Every tag is logged in the codebase's release page under `docs/releases/`.

**Design first.** A large feature or engineering plan is an RFC in `docs/engineering/rfc/`; a decision is an ADR in `docs/engineering/adr/`. Work items are `RFC-PPNN/Wnn`: the same id is the issue prefix, the branch name and the `Refs:` footer of the commit.

**API.** REST v1 under `/ag/api/v1`, JSON, bearer tokens issued at login (password or login QR), device binding per phone, idempotency keys on every write, 214 routes documented per app screen. The MCP server exposes the same business operations as typed tools with previews.

**Conventions.** Plain language in docs and commits, absolute dates, no secrets in repositories (a `.secrets/` folder is git-ignored and hooks scan for keys), tests beside the code they cover, screenshots as evidence on every fix.

**Security.** TLS everywhere, certificate-verified database connections, per-role database accounts for anyone who touches data directly, rate limits on public endpoints, an audit store with sealed chains, and no third-party analytics or advertising SDK in the field app.

## Working with Betopia

Business Brain 24 is delivered as a project: discovery and business requirements, phased roll-out by module, user training through Sohay and on site, then an annual maintenance contract with a named support channel.

- Company: [Betopia Limited](https://betopialimited.com), Dhaka, Bangladesh
- Technical authority: Mosthofa Imran, CTO
- Support and partnerships: support@betopialimited.com

<p align="center">
  <img src="https://raw.githubusercontent.com/business-brain-24/.github/main/profile/assets/betopia-limited.png#gh-light-mode-only" alt="Betopia Limited" width="140">
  <img src="https://raw.githubusercontent.com/business-brain-24/.github/main/profile/assets/betopia-limited-dark.png#gh-dark-mode-only" alt="Betopia Limited" width="140">
</p>
<p align="center"><sub>Business Brain 24 and Beto AI are products of Betopia Limited. Made with coffee and love by the CTO Office.</sub></p>
