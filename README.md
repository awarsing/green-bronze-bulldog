# RoleFinder Serverless Architecture Briefing

## Purpose

This document evaluates the current RoleFinder MVP and proposes a serverless architecture suitable for a commercial product. The goal is to move from a working prototype to a secure, scalable, multi-tenant SaaS offering.

---

## What this is -- and what it isn't

### Monitoring infrastructure for your job search

RoleFinder is not a job board. It's not competing with LinkedIn, Indeed, or Glassdoor. Those are **marketplaces** where the employer is the customer, the job seeker is the product, and revenue comes from recruiter tools and promoted listings.

RoleFinder is **monitoring infrastructure**. The user picks companies they care about, defines what they're looking for, and the system watches those career pages daily, scores new listings against the user's profile, and delivers a digest. The analogy is Google Alerts, not Google Search -- you configure it once, and it tells you when something relevant appears.

The daily experience for the user:
1. Check email (or dashboard)
2. See 3-5 new high-scoring jobs at companies they care about, with explanations
3. Open the ones that look promising on the company's career page
4. Done in 5 minutes

vs. what they do today:
1. Open LinkedIn, Indeed, Glassdoor, and 10 company career pages in separate tabs
2. Search the same keywords again
3. Scroll past jobs they've already seen
4. Manually evaluate whether each one is a fit
5. 30-60 minutes, repeated daily

$5-12/month to reclaim that time during an active job search is a straightforward value trade.

### Why this isn't another Indeed

| | Job boards (Indeed, LinkedIn, Glassdoor) | RoleFinder |
|---|---|---|
| Who pays | Employers | Job seekers |
| Incentive | Keep you searching (engagement = revenue) | Find the right job fast (good matches = retention) |
| Discovery model | You search, they show results | You configure once, it monitors for you |
| Ranking | Paid placement + engagement signals | Pure AI scoring against your stated preferences |
| Data model | Your resume/profile is sold to recruiters | Your profile is private, never shared, never stored as a resume |
| Freshness | Aggregated, often days or weeks delayed | Direct from career pages, same-day |
| Scope | Only jobs posted on their platform | Any company with a career page, anywhere |

The key difference is **incentive alignment**. Job boards make money when employers post. Their business model requires employer participation. RoleFinder goes around the marketplace entirely by reading the source -- the company's own career page. LinkedIn can't easily copy this because their revenue depends on employers posting jobs on LinkedIn, not on users bypassing LinkedIn.

### What the moat is

The technology itself (Jina + Gemini + Supabase) is not a moat. Anyone can wire up APIs. What becomes defensible over time follows the **Goodreads model** -- the community-generated data becomes the product, not the features (see Competitive Landscape -- The Goodreads parallel for the full analogy):

1. **The company directory (network effect)**: Every user who adds a company with a verified career URL makes the product better for the next user. Over time this becomes a crowd-sourced, validated database of thousands of career page URLs. No new competitor starts with this data. Simplify has 20,000+ companies but doesn't expose their directory as a community asset.

2. **The scoring criteria community**: If users can share, fork, and rate scoring configurations ("Senior Backend Engineer template", "Career Pivoter template", "New Grad template"), the best-performing criteria rise to the top. "This template helped 340 users find EXCELLENT_MATCH jobs" is social proof no competitor can fabricate.

3. **Company-first discovery**: Job boards are keyword-first. RoleFinder is company-first. "I want to work at these 20 companies -- tell me when something opens up" is a fundamentally different use case than "show me software engineer jobs in Austin." Both are valid. RoleFinder owns the first one.

4. **Privacy as positioning**: "We never store your resume. We never share your profile with recruiters. We never sell your data." Every competitor in the auto-apply and aggregator categories requires a full resume upload. In a world where LinkedIn profits from recruiter access to user profiles, this resonates -- especially with EU users where data privacy awareness is high.

### Honest risks

- **LinkedIn could add monitoring features.** But their business model fights it -- they make money from employers posting on LinkedIn, not from users reading career pages directly. Classic innovator's dilemma.
- **Low switching costs.** A user who finds a job cancels. That's success, not churn. The metric that matters is activation rate (free to paid conversion), not long-term retention of individual users. Job seekers arrive in waves.
- **"AI matching" is a crowded claim.** Every job board says they use AI. The differentiation has to be demonstrated, not claimed. Transparent scoring -- showing exactly why each job scored the way it did -- is how you prove it's real, not marketing.

### How to position it

Don't position against Indeed or LinkedIn. Position against **the daily routine of manually checking career pages**. The competitor isn't a platform -- it's the user's open browser tabs.

- "Your career pages, monitored daily, scored automatically."
- "Stop refreshing career pages. Start getting matched."
- "Company-first job matching. No recruiters. No resume sharing."

---

## Competitive Landscape

### The market categories

The AI job search space is crowded but segmented. Understanding where RoleFinder sits -- and where it deliberately doesn't -- is essential to positioning.

**Category 1: Auto-apply tools (spray and pray)**

These tools upload your resume and mass-apply to hundreds of jobs automatically. They optimize for volume, not fit.

| Tool | Pricing | What they do | Key weakness |
|---|---|---|---|
| [Sonara](https://www.sonara.ai) | $5.95/mo (annual), $23.95/mo (monthly) | Scans listings, auto-fills applications, submits on your behalf | Mixed reviews: 50%+ submission failures, poor matching (IT PM search returns clerks and doctors), no per-job resume optimization |
| [Jobright](https://jobright.ai) | Free tier + paid | AI copilot, matched roles, resume tailoring, real-time alerts | US-only coverage. Users want to preview jobs before uploading resume |
| [AIApply](https://aiapply.co) | Paid tiers | Auto-apply with tailored resume/cover letter per job | Quantity over quality approach |
| [LoopCV](https://www.loopcv.pro) | Free + paid | Auto-apply daily based on preferences | Users report low response rates from auto-applied jobs |
| [Final Round AI](https://www.finalroundai.com/ai-job-hunter) | Paid | 24/7 auto-find and auto-apply | Same spray-and-pray model |
| [LiftmyCV](https://www.producthunt.com/products/liftmycv) | Paid | AI agent applies on your behalf | Early stage product |

**RoleFinder is NOT in this category.** Auto-apply tools have a growing reputation problem -- employers increasingly detect and filter mass AI applications, and the approach devalues the candidate. RoleFinder surfaces and scores; the user decides what to pursue and applies themselves on the company's own career page.

**Category 2: Job search management / trackers**

Organizational tools for people who are already finding jobs through other channels.

| Tool | Pricing | What they do | Key weakness |
|---|---|---|---|
| [Huntr](https://huntr.co) | Free + paid | Track applications, AI resume/cover letter, browser extension | Discovery is manual -- you still find the jobs yourself |
| [Teal](https://www.producthunt.com/products/career-assist-by-teal) | Free + paid | Application tracker, resume builder, LinkedIn optimizer | No monitoring, no scoring, no discovery |
| [JobHunnt](https://jobhunnt.com) | Paid | Resume builder, cover letters, interview prep | Toolbox, not monitoring |

RoleFinder overlaps slightly here (job history, saved listings) but the core value is different. These are organizational tools for an existing search. RoleFinder is a discovery tool that initiates the search.

**Category 3: Job board aggregators with AI matching**

| Tool | Pricing | What they do | Key weakness |
|---|---|---|---|
| [Simplify](https://simplify.jobs) | Free + premium | Indexes 20,000+ company career pages directly, AI matching, autofill browser extension, community salary/interview insights | **Closest competitor.** They scrape career pages. But matching is keyword-based, not AI-scored-against-a-structured-profile. No daily digest model. No transparent per-job scoring with reasoning |
| [OpenJobs AI](https://www.producthunt.com/products/openjobs-ai) | Free (unclear model) | Chat-based job discovery, real-time monitoring of public postings | Early stage, unclear monetization |

**Simplify is the one to study.** They already index 20,000+ career pages and take jobs "directly from each company's native job board." Their differentiator is the browser extension (autofill) and community-driven insights (salaries, interview questions). They explicitly claim "we don't take orders from HR departments" -- same incentive alignment RoleFinder has. The gaps Simplify leaves open: no personalized AI scoring with transparent reasoning, no daily digest delivery model, no structured profile (they require a resume upload).

**Category 4: Page change detection (dumb monitoring)**

| Tool | Pricing | What they do | Key weakness |
|---|---|---|---|
| [Visualping](https://visualping.io) | Free (150 checks/mo), $10-50/mo paid | Monitors any web page for visual/text changes, alerts via email/Slack | No AI, no job extraction, no profile matching. Just "this page changed." User still has to read the page and evaluate manually |
| Google Alerts | Free | Keyword alerts across the web | Not career-page-specific, extremely noisy |
| LinkedIn Job Alerts | Free | Notifications for new jobs matching keywords | Platform-locked to LinkedIn-posted jobs only. Keyword-only. Employer-biased ranking (promoted listings) |

RoleFinder sits between category 3 and 4 -- it monitors pages like Visualping but understands the content like Simplify, and adds personalized AI scoring that neither provides.

### Where the gap is

| Feature | Auto-apply tools | Trackers | Simplify | Visualping | **RoleFinder** |
|---|---|---|---|---|---|
| Monitors career pages directly | No (aggregated listings) | No | Yes | Yes | **Yes** |
| AI scores against your profile | Basic keyword matching | No | Keyword match | No | **Yes, with transparent reasoning** |
| Daily email digest | Some | No | No | Change alerts only | **Yes** |
| Explains why each job scored | No | N/A | No | N/A | **Yes (per-dimension breakdown)** |
| Company-first discovery | No | N/A | Partially | Yes | **Yes** |
| No resume stored | No (all require upload) | Most require resume | Requires resume | N/A | **Yes** |
| Privacy-first (no recruiter sharing) | No | Varies | Claims yes | N/A | **Yes** |

The open space: **personalized AI-scored monitoring of company career pages with transparent reasoning and no resume storage.** No existing product does exactly this.

### The Goodreads parallel

The network effect here follows the same pattern as [Goodreads](https://www.goodreads.com). When Goodreads launched, there were plenty of places to buy books (Amazon), review books (Amazon), and discover books (bestseller lists). Goodreads didn't compete with any of them directly. It built a **community-curated library** where:

- Every user who added a book enriched the catalog for everyone
- Every rating and review improved discovery for the next reader
- Shared "shelves" (reading lists) became a social discovery mechanism
- Over time, the crowd-sourced data became the product -- more valuable than any single feature

Amazon eventually bought Goodreads for $150M, not for the technology (trivial to rebuild), but for the **community-generated data asset** that couldn't be replicated without the users.

RoleFinder's version of this:

| Goodreads | RoleFinder |
|---|---|
| Users add books to the catalog | Users add companies with verified career URLs to the directory |
| Star ratings + text reviews | Job scoring criteria templates (dimensions, weights, deal-breakers) |
| Shared reading lists ("shelves") | Shared scoring profiles ("Senior Backend Engineer", "Career Pivoter", "New Grad") |
| "Readers also enjoyed..." | "Users monitoring this company also monitor..." |
| Community Q&A about books | Community notes about companies ("Greenhouse ATS, posts fill fast", "Remote-friendly despite listing on-site") |

The directory of verified career page URLs is the seed. It starts with a curated set built during Phase 0, grows with every user who adds a company, and becomes self-reinforcing: the more companies in the directory, the easier onboarding is, the more users sign up, the more companies get added. This is the flywheel that Simplify has started building with their 20,000+ company index -- but they don't expose it as a community asset. RoleFinder can.

The scoring criteria community is the second layer. When users share, fork, and rate scoring configurations, the best-performing templates rise to the top. "This Senior Backend Engineer template helped 340 users find EXCELLENT_MATCH jobs" is social proof that no competitor can fabricate. It's Goodreads ratings for job search strategies.

### Pricing context

| Competitor | Price |
|---|---|
| Sonara (annual) | $5.95/mo |
| Simplify | Free + premium (unclear pricing) |
| Visualping (50 pages) | $14/mo |
| LinkedIn Premium | $30/mo |
| Jobright | Free + paid |
| **RoleFinder Starter** | **$5/mo** |
| **RoleFinder Pro** | **$12/mo** |

The $5/$12 pricing is competitive: below LinkedIn Premium, in line with Sonara's annual rate, and offers transparent AI scoring that none of them provide.

### What to learn from each competitor

| Competitor | What they got right | What to avoid |
|---|---|---|
| **Simplify** | Direct career page scraping, "we don't take orders from HR," community insights, open-source job lists on GitHub | Requiring a full resume upload. Keyword matching instead of real AI scoring |
| **Sonara** | Set-it-and-forget-it daily digest model, low annual price point | Auto-apply (reputation risk, high failure rates). Deceptive trial-to-subscription pricing |
| **Visualping** | Simple mental model ("monitor this page"), email/Slack alerts, clear pricing | No intelligence layer. User still has to read and evaluate manually |
| **Huntr** | Browser extension for capturing jobs you find, clean application tracker | No discovery. Organizational tool, not a monitoring tool |
| **Goodreads** (analogy) | Community-curated catalog as the moat, not features. Social proof via ratings/reviews. Acquired for the data, not the code | Don't over-feature early. The community data is the product |

---

## Current State: n8n MVP

### What works

The existing RoleFinder project demonstrates a validated concept:

- Four-workflow pipeline (discover, evaluate, score, deliver) is sound
- AI-powered job scoring with customizable dimensions is the core differentiator
- Profile externalization (candidate data in DB, not hardcoded) shows good instincts
- Fault tolerance at the company and job level is well thought out

### What limits it as a commercial product

| Concern | Detail |
|---|---|
| **Single-tenant** | One n8n instance per user, or shared workflows with no isolation |
| **No authentication** | No user login, no session management, no access control |
| **Data isolation** | n8n Data Tables have no row-level security; all users see all data |
| **Secret management** | API keys stored in n8n's credential store, not encrypted at rest per-tenant |
| **Cost scaling** | n8n Cloud pricing + Apify + Claude adds up fast per-user |
| **Vendor lock-in** | Business logic lives in n8n's proprietary workflow JSON format |
| **No billing integration** | No way to meter usage, charge customers, or enforce plan limits |
| **Deployment** | n8n Cloud or self-hosted Docker; neither is zero-ops for a SaaS |
| **No deduplication** | Every run re-fetches, re-scores, and re-emails all jobs in the 24h Apify window. If a posting persists across days, the user sees it in every digest. The README acknowledges this ("Clear email_queue before runs") and cross-run dedup is planned for v0.8.0, but no implementation exists as of v0.6.0. The only guard is Apify's `timeRange: "24h"` parameter, which is time-window filtering, not dedup |

None of these are criticisms of the MVP. They're the gap between "this works for me" and "I can charge people for this."

### Security audit: Code review findings

All four workflow JSON files (Main.json, Loop_Companies.json, Loop_Jobs.json, Send_Email.json) were reviewed directly from the repository. Findings:

**No authentication exists anywhere in the pipeline.**

| Workflow | Auth checks | Session validation | User verification |
|---|---|---|---|
| Main.json | None | None | None |
| Loop_Companies.json | None | None | None |
| Loop_Jobs.json | None | None | None |
| Send_Email.json | None | None | None |

Specific findings:

- **Main.json** loads all profiles from the data table with no filter. The only access control is "whoever has access to the n8n instance."
- **Send_Email.json** accepts the recipient email from whatever the parent workflow passes in. No verification that the email belongs to the profile owner. Any caller with a `workflow_run_id` can trigger an email to any address with that run's job data.
- **Loop_Jobs.json** passes resume text (`_context_resume_text`) through the entire pipeline as plaintext. No encryption, no field-level access control.
- **Data table queries** filter by `profile_id` or `workflow_run_id`, but there is no check that the caller owns that profile. Trust is implicit -- the assumption is that only the n8n instance itself calls these workflows.
- **API credentials** (Anthropic, Apify, SMTP) are referenced by name and stored in n8n's credential vault, not in the JSON. This part is handled correctly by n8n's standard pattern.

**Risk assessment**: For a single-user self-hosted deployment, the n8n instance itself is the security boundary, and this is acceptable. The moment a second user is added -- or this is exposed as a service -- there is no data isolation, no email ownership verification, no rate limiting, and resume data flows unencrypted across all four workflows.

### Open issues analysis

As of February 2026, the repository has **22 open issues and 14 closed issues**. The pattern reveals where the developer is comfortable (n8n-native configuration tasks) and where the platform is becoming a ceiling.

**Closed issues (14)** -- all solvable within n8n:
- Scheduling (#1), email migration (#33), n8n version upgrades (#36, #29), externalizing config (#9, #8, #35), data table updates (#21), multi-user support (#18), documentation (#15), zero-job handling (#28), summary reports (#37)
- LinkedIn integration (#30) closed as "Not planned" -- correct call given LinkedIn's auth wall

**Open issues -- Critical bugs (6, all high priority):**

| # | Issue | Root cause |
|---|---|---|
| 42 | Match category counts are wrong | AI scoring logic has bugs -- the core value proposition is unreliable |
| 26 | JSON.parse fails on AI response | Claude returns malformed JSON and the pipeline crashes with no recovery |
| 25 | JSON.parse fails in Build Request | Same class of bug -- no input validation anywhere in the pipeline |
| 24 | `$json` mode mismatch in Log Error | The error logging node itself is broken, preventing debugging of other failures |
| 27 | No retry logic on AI evaluation | One API timeout or rate limit kills the entire job batch |
| 5 | No testing suite | No way to verify that any fix actually works; no regression protection |

These are all **code-level problems** (JSON parsing, error handling, input validation) that are difficult to solve in a visual workflow editor. In a standard codebase, each would be a try/catch block or a unit test.

**Open issues -- Infrastructure limitations (5, medium priority):**

| # | Issue | What it means |
|---|---|---|
| 11 | Self-host n8n + database | Developer recognizes n8n Cloud costs are unsustainable |
| 10 | Batch Apify requests for cost reduction | Apify costs are a concern at scale |
| 4 | Database needs indexes and constraints | n8n Data Tables don't support real database optimization |
| 6 | No monitoring or alerting | n8n provides no built-in observability |
| 2 | No API quota monitoring | Spend is untracked; risk of surprise bills |

**Open issues -- Feature aspirations (6, low-medium priority):**

| # | Issue | Why it's stuck |
|---|---|---|
| 19 | Advanced job discovery integrations | Locked into Apify's scraper model |
| 17 | AI evaluation improvements | Limited by n8n Code node debugging experience |
| 16 | Historical analysis dashboard | n8n Data Tables can't do analytical queries |
| 14 | Multi-channel delivery (Slack, etc.) | Possible in n8n but adds more fragile workflow nodes |
| 13 | Email template enhancements | HTML templating in n8n Code nodes is cumbersome |
| 7 | Profile management workflow | Building a UI in n8n -- fighting the tool |

**Remaining open issues (5):**

| # | Issue | Category |
|---|---|---|
| 41 | CSS Grid breaks stats layout in Outlook | Email rendering bug |
| 23 | Implement global error trigger workflow | Error handling infrastructure |
| 22 | Add timeout settings to Execute Workflow nodes | Reliability |
| 12 | Enhance error handling and retry logic | Reliability (duplicate of #27) |
| 3 | Send "no jobs today" email if no jobs found | UX completeness |

**What the issue tracker reveals**: The developer has hit the ceiling of n8n as a platform. The closed issues are all configuration-level changes. The open issues are overwhelmingly code-level problems (JSON parsing, error handling, input validation, database optimization, monitoring) that n8n makes hard or impossible to solve properly. The six critical bugs -- particularly #42 (broken scoring counts) and #24 (broken error logging) -- indicate the core pipeline is unreliable in its current state.

The proposed serverless architecture resolves every open infrastructure issue by design: try/catch for JSON parsing, Postgres indexes and constraints, built-in monitoring via Supabase dashboard, standard testing frameworks, and structured error handling in TypeScript rather than n8n Code nodes.

---

## Proposed Architecture

### Stack

**Supabase** (hosted Postgres + Auth + Edge Functions + Cron + Storage)
- Auth -- User signup/login, OAuth providers, JWT sessions
- Postgres -- All data, with Row-Level Security per user
- Edge Functions -- Deno/TypeScript, serverless, per-invocation billing
- pg_cron -- Available for simple scheduled tasks if needed
- Vault -- Encrypted secret storage for user API keys (BYOK support)
- Realtime -- Optional: live dashboard updates via websockets

**Jina.ai Reader API**
- Career page fetching -- any URL to clean markdown, ATS-agnostic

**Gemini 2.5 Flash Lite** (via Google AI)
- Job extraction + scoring in a single LLM call
- Context caching for repeat scoring against the same user profile (reduces input cost to $0.01/1M tokens)
- Users can bring their own API key (BYOK) stored encrypted in Supabase Vault

**Vercel** (frontend hosting)
- Next.js or static site for the user dashboard, onboarding, and company management
- Supabase Auth handles all authentication; Vercel serves the frontend that calls it

**Upstash** (serverless Redis + QStash)
- Redis -- session caching, rate limiting, static page caching (reduces Vercel bandwidth)
- QStash -- message queues for pipeline orchestration (fan-out job discovery, retry failed steps)
- Pay-as-you-go: $0.20 per 100K Redis commands, $1 per 100K QStash messages

**Resend**
- Transactional email for job digests (outbound)
- Inbound email forwarding for support inquiries and compliance requests (e.g., data deletion, GDPR subject access)
- Free tier: 3,000 emails/month, 1,000 contacts

### Why these choices

**Supabase** over raw AWS/GCP:
- Postgres, auth, edge functions, cron, storage, and realtime in one platform
- Free tier covers early customers (500MB DB, 500K edge invocations/month)
- Row-Level Security is native to Postgres, not a bolted-on abstraction
- Supabase CLI enables local development and CI/CD
- No ops burden; managed infrastructure

**Jina Reader** over Apify:
- Tested successfully against LinkedIn, Greenhouse, Lever, Apple, Xing, Reddit
- ATS-agnostic: reads any career page URL, no per-platform scrapers to maintain
- ~$0.02 per 1M tokens ($20 per 1B tokens) after the 10M free token allowance. At ~7,000 tokens per career page, that is roughly 143,000 page reads per $20. Note: Jina's pricing is not prominently published and changed in May 2025 -- verify against their dashboard before launch
- Already proven in our testing (see test results appendix)

**Gemini 2.5 Flash Lite** over Claude Sonnet:
- Job scoring is classification/structured-output, not creative reasoning
- Input: $0.10/1M tokens, Output: $0.40/1M tokens -- roughly 40-80x cheaper than Sonnet
- Context caching: $0.01/1M tokens for cached input + $1/1M tokens/hour storage. The user's profile + scoring criteria + system prompt can be pre-cached as a named CachedContent object before the fan-out, then referenced by all parallel company calls. However, at ~800 tokens the profile is below Gemini's minimum cache size (typically 4,096 tokens for Flash models). In practice, the savings from caching are minimal at this token count -- the main cost driver is the career page content and output tokens, not the repeated profile
- Supports structured JSON output natively
- Fast inference (~1-2s per job evaluation)
- Free tier available for development and testing
- BYOK support: users who already have a Google AI API key can use their own, reducing operator costs to zero for those users. Key stored encrypted in Supabase Vault

**Vercel** for the frontend:
- Hobby tier is free but non-commercial. Pro tier is $20/month -- required for a commercial product
- Includes 1TB bandwidth, serverless functions, preview deployments for every PR
- Redis caching via Upstash reduces bandwidth usage and Vercel load for static/semi-static pages
- Required for multi-tenancy -- users need a UI to sign up, build their profile, manage companies, and view results. Authentication (Supabase Auth) requires a frontend to collect credentials and manage sessions

**Upstash** (Redis + QStash):
- Pay-as-you-go with generous free tiers (Redis: 500K commands/month free, QStash: 1,000 messages/day free)
- Redis caches session data, rate-limit counters, and rendered page fragments -- reduces database load and Vercel bandwidth
- QStash replaces pg_cron for pipeline orchestration: fan-out one message per company, built-in retries with exponential backoff, dead letter queues for failed steps. More reliable than database-triggered workflows
- Costs are negligible at this scale (pennies/month for typical usage)

**Resend** for email:
- Free tier: 3,000 emails/month outbound, 1,000 contacts
- Inbound email forwarding: route support@yourdomain and privacy@yourdomain to your application via webhooks (email.received event) for customer inquiries and compliance requests (GDPR subject access, deletion requests). Inbound emails count against your plan quota alongside outbound
- Paid tier: $20/month for 50,000 emails if free tier is exceeded

---

## Data Model

### Core tables

```sql
-- Users managed by Supabase Auth (auth.users)
-- All tables below have RLS policies enforcing user_id = auth.uid()

create table profiles (
  id uuid primary key references auth.users(id) on delete cascade,
  full_name text,
  email text not null,

  -- Structured profile fields (collected via onboarding form, not raw resume)
  target_titles text[] not null,       -- e.g. {"Senior Backend Engineer","Staff Engineer"}
  skills_desired text[],               -- e.g. {"Python","Go","distributed systems"}
  skills_excluded text[],              -- e.g. {"PHP","WordPress"}
  seniority_level text,                -- e.g. "senior", "staff", "principal"
  locations text[],                    -- e.g. {"Remote US","NYC","SF"}
  work_arrangement text,               -- "remote", "hybrid", "onsite", "any"
  compensation_floor int,              -- e.g. 180000 (annual, USD)
  deal_breakers text[],                -- e.g. {"defense/military","crypto"}
  preferences_text text,               -- free-text: "small team, high ownership, series B-C"

  -- Scoring configuration
  scoring_criteria jsonb not null,     -- AI scoring dimensions + weights

  -- Account settings
  email_frequency text default 'daily', -- daily, weekly, manual
  plan text default 'free',            -- free, starter, pro, power
  gemini_api_key_vault_id text,         -- reference to Supabase Vault secret (BYOK, optional)
  gdpr_consent_at timestamptz,         -- when user consented to data processing
  ai_scoring_consent_at timestamptz,   -- when user consented to automated scoring (Art. 22)
  created_at timestamptz default now()
);

create table resume_upload_events (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users(id) on delete cascade not null,
  fields_extracted text[],             -- which profile fields were populated, e.g. {"target_titles","skills_desired"}
  extraction_succeeded boolean not null,
  raw_text_discarded boolean not null default true,  -- must always be true; auditable proof
  created_at timestamptz default now()
  -- NOTE: raw resume text is NEVER stored in this table or anywhere in the database
);

create table companies (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users(id) on delete cascade not null,
  name text not null,
  career_url text not null,            -- the URL Jina Reader will fetch
  ats_type text,                       -- greenhouse, lever, workday, custom (informational)
  active boolean default true,
  last_checked_at timestamptz,
  last_content_hash text,              -- SHA-256 of Jina Reader output; skip Gemini if unchanged
  created_at timestamptz default now()
);

create table jobs (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users(id) on delete cascade not null,
  company_id uuid references companies(id) on delete cascade not null,
  run_id uuid not null,                -- groups jobs from the same run
  external_id text,                    -- dedup key (title + company + location hash)
  title text not null,
  location text,
  department text,
  salary_range text,
  url text,                            -- link to the job posting
  raw_content text,                    -- original extracted text
  ai_scores jsonb,                     -- { dimensions: [...], overall: 8.2 }
  recommendation text,                 -- EXCELLENT_MATCH, GOOD_MATCH, CONSIDER, POOR_FIT
  email_sent boolean default false,
  discovered_at timestamptz default now()
);

create table runs (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users(id) on delete cascade not null,
  status text default 'running',       -- running, completed, failed
  companies_checked int default 0,
  jobs_found int default 0,
  jobs_scored int default 0,
  email_sent boolean default false,
  error_log jsonb,
  started_at timestamptz default now(),
  completed_at timestamptz
);
```

### Row-Level Security

Every table gets a policy like:

```sql
alter table companies enable row level security;

create policy "Users can only access their own companies"
  on companies for all
  using (user_id = auth.uid());
-- Note: profiles uses (id = auth.uid()) since profiles.id references auth.users(id) directly
```

This is the single biggest security improvement over the n8n version. A user can never see, modify, or delete another user's data, even if there's a bug in the application code. The database enforces it.

---

## Pipeline Design

### Overview

**Step 1: orchestrate-run** (edge function, triggered daily by QStash cron)
- For each user due for a run: creates a `runs` record, queries active companies, sets `run:{run_id}:remaining` in Redis to the company count, then fans out one QStash message per company (each message contains the user_id, company_id, and run_id). This is the only sequential step -- everything after it runs in parallel

**Step 2: discover-and-score** (edge function, one invocation per company)
- Triggered per-user, per-company via QStash fan-out
- Calls Jina Reader -> career page markdown
- Hashes the markdown (SHA-256) and compares against `companies.last_content_hash`. If identical, the career page hasn't changed since the last run -- skip the Gemini call entirely, decrement the Redis counter, and return early. This is the common case: most career pages don't change daily
- If the hash differs (page changed): calls Gemini with a single combined prompt to extract job listings AND score each against the user's structured profile in one LLM call (see prompt below). Updates `last_content_hash` with the new hash
- Deduplicates against existing jobs via `external_id` (title + company + location hash). Only new jobs are inserted and scored. This is a two-layer fix for a gap in the n8n version, which has no dedup and re-scores every job every run: the content hash avoids redundant LLM calls when a page hasn't changed, and the `external_id` check avoids duplicate digest entries when individual jobs persist across page updates
- Inserts only new jobs with scores, recommendation, and reasoning into `jobs` table

**Step 3: send-digest** (edge function)
- Triggered after all company invocations complete. Implementation: each discover-and-score function decrements a counter in Redis (`DECR run:{run_id}:remaining`). When the counter hits zero, the last function publishes a QStash message to invoke send-digest. This is a simple atomic counter pattern -- no polling, no batch API needed
- Queries all jobs for this run, sorted by score descending
- Groups into sections: EXCELLENT_MATCH, GOOD_MATCH, CONSIDER, then POOR_FIT (collapsed/summarized)
- Builds HTML email
- Sends via Resend/SMTP
- Marks jobs as email_sent = true
- Updates the run record with final stats

### Why three functions

1. **Execution time limits**: Supabase Edge Functions have a 150s wall-clock limit (free tier) or 400s (Pro). Splitting ensures each function completes within budget.
2. **Retry granularity**: If email sending fails, you don't re-run discovery. Each step is independently retryable via QStash dead letter queues.
3. **Observability**: The `runs` table tracks exactly where a pipeline succeeded or failed. orchestrate-run creates the record, discover-and-score updates job counts, send-digest marks completion.
4. **Parallelism**: Discovery+scoring fans out across companies concurrently via QStash. One function invocation per company, all running in parallel. The n8n version processes companies sequentially.

### Combined extraction and scoring prompt

A single Gemini call both extracts job listings from Jina's markdown AND scores them against the user's profile. This halves the number of LLM calls compared to separate extract and evaluate steps.

Prompt structure:

```
You are a job matching assistant. Given:
1. A career page (markdown)
2. A candidate's structured profile and scoring criteria

Candidate profile:
- Target titles: ["Senior Backend Engineer", "Staff Engineer"]
- Desired skills: ["Python", "Go", "distributed systems"]
- Excluded skills: ["PHP", "WordPress"]
- Seniority: Senior / Staff
- Locations: ["Remote US", "NYC", "SF"]
- Work arrangement: Remote
- Compensation floor: $180k
- Deal-breakers: ["on-site only", "defense/military"]
- Preferences: "small team, high ownership, series B-C"

Scoring dimensions:
- role_alignment (weight: 0.3): How well does the title and description match target titles?
- seniority_fit (weight: 0.25): Does the role match the candidate's seniority level?
- location_match (weight: 0.2): Does the location/remote policy match?
- skill_overlap (weight: 0.25): How many desired skills are relevant? Any excluded skills present?

Extract all job listings from the career page. Score each one against the profile.
Immediately disqualify jobs that match any deal-breaker.

Return JSON:
{
  "jobs": [
    {
      "title": "...",
      "location": "...",
      "department": "...",
      "url": "...",
      "scores": {
        "role_alignment": 8,
        "seniority_fit": 7,
        "location_match": 9,
        "skill_overlap": 6
      },
      "overall_score": 7.5,
      "recommendation": "GOOD_MATCH",
      "reasoning": "Strong backend role with Python/Go stack. Remote-friendly. Series C company.
        Slightly below target seniority -- listed as 'Senior' not 'Staff'."
    }
  ]
}
```

Note: the prompt contains no PII -- no name, no employer history, no education, no contact details. Only the user's stated job preferences and skills. This is the data minimization principle in practice.

The full pipeline is three edge functions: orchestrate-run (cron-triggered, creates run, initializes counter, fans out), discover-and-score (one per company, parallel), and send-digest (triggered by the last discover-and-score to finish).

---

## Security Model

### Authentication

Supabase Auth handles:
- Email/password signup with confirmation
- OAuth providers (Google, GitHub) for frictionless onboarding
- JWT tokens with configurable expiry
- Password reset flows
- Multi-factor authentication (TOTP)

The frontend never touches the database directly with raw credentials. All requests go through Supabase's client library, which attaches the JWT automatically.

### Authorization

Row-Level Security (RLS) on every table. Even if an attacker obtains a valid JWT for User A, they cannot access User B's data. This is enforced at the Postgres level, below the application layer.

### Secrets

- User-facing API keys (if users bring their own Gemini key): stored in Supabase Vault, encrypted at rest, accessed only within edge functions
- Service-level secrets (Jina API key, Gemini API key, Resend API key): stored as Supabase Edge Function secrets, never exposed to the client

### Data privacy

- No raw resume text is stored. The structured profile replaces it (see UX Gap Analysis -- The resume problem).
- If the optional resume-upload shortcut is used, raw text is processed in-memory within the edge function, structured fields are extracted, and the original is discarded. It is never written to the database. This process is auditable: a `resume_upload_events` log records the timestamp, user ID, whether extraction succeeded, which fields were populated, and a confirmation that the raw text was discarded -- without storing the text itself.
- AI prompts include only the structured profile (target titles, skills, preferences) -- not PII like names, addresses, or employment dates.
- GDPR: users can delete their profile via a single action, which cascades to all companies, jobs, and runs (foreign key ON DELETE CASCADE). See UX Gap Analysis -- GDPR section for full compliance requirements.
- A Data Protection Impact Assessment (DPIA) must be conducted before launch (GDPR Article 35).
- Data Processing Agreements (DPAs) must be executed with all sub-processors: Supabase, Google (Gemini), Jina, Resend.

### Rate limiting

- Supabase has built-in rate limiting on Auth endpoints
- Edge functions can check `profiles.plan` to enforce per-tier limits (e.g., free = 5 companies, starter = 15, pro = 50, power = 100)
- Jina and Gemini API keys have their own rate limits as a backstop

---

## Cost Analysis

### Assumptions

Baseline user: monitors 20 companies, daily digest, ~15 jobs discovered per company per run.

- Jina Reader: ~7,000 tokens per career page. 20 companies = 140,000 tokens/day per user
- Gemini 2.5 Flash Lite: combined extract+score prompt per company -- ~7,000 tokens (career page markdown) + ~800 tokens (profile + scoring criteria + system prompt) = ~7,800 input tokens per call. Output at ~300 tokens per job x 15 jobs = ~4,500 output tokens per call. The ~800-token profile/criteria portion is repeated across all company calls in a run and benefits from context caching
- QStash: ~22 messages/day per user (1 trigger + 20 discover-and-score + 1 send-digest)
- Redis: ~200 commands/day per user (session, rate limits, page cache)
- Resend: 1 email/day per user (digest), plus occasional inbound

### Per-user variable cost (monthly)

| Component | Calculation | Cost/user/month |
|---|---|---|
| Jina Reader | 140K tokens/day x 30 = 4.2M tokens/month at $0.02/1M tokens | ~$0.08 (effectively free) |
| Gemini input | 20 companies x 7.8K tokens x 30 days = 4.68M tokens at $0.10/1M | ~$0.47 |
| Gemini output | 20 companies x 4.5K tokens x 30 days = 2.7M tokens at $0.40/1M | ~$1.08 |
| QStash | ~660 messages/month (22/day x 30) at $1/100K messages | $0.0066 (free tier covers it) |
| Redis | ~6,000 commands/month at $0.20/100K commands | $0.012 (free tier covers it) |
| Resend | 30 emails/month | Free tier (3,000/month shared) |
| Supabase Edge Functions | 21 invocations/day (20 discover + 1 digest) x 30 = 630/month | Free tier (500K/month shared) |
| **Total variable cost per user (baseline)** | | **~$1.63/month** |

The content-hash optimization (see pipeline design) reduces Gemini costs in practice. If 75% of career pages are unchanged on a given day, Gemini calls drop by 75% and the effective per-user cost falls to ~$0.47/month. The $1.63 figure is the worst case where every page changes every day.

### Fixed monthly costs (operator pays regardless of user count)

| Component | Cost | Notes |
|---|---|---|
| Vercel Pro | $20/month | Required for commercial use. 1TB bandwidth, serverless functions |
| Supabase Pro (when needed) | $0 (free) to $25/month | Free tier handles early growth. Pro at ~500+ active users |
| Resend paid tier (when needed) | $0 to $20/month | Free tier covers up to ~100 daily-digest users |
| Upstash | $0 | Free tiers cover early usage |
| Custom domain + DNS | ~$1/month | Cloudflare or similar |
| **Total fixed cost** | **$21-66/month** | |

### Scaling scenarios (worst case: all users at baseline)

This table assumes every user runs the baseline (20 companies, daily digest). In practice, 80% of users are on the free tier (5 companies, weekly), so real costs are much lower -- see revenue projections for tier-mix costs.

| Scenario | 10 users | 100 users | 500 users | 1,000 users |
|---|---|---|---|---|
| Variable cost | $16.30 | $163 | $815 | $1,630 |
| Fixed cost | $21 | $21 | $66 | $66 |
| **Total (no BYOK)** | **$37** | **$184** | **$881** | **$1,696** |
| BYOK users reduce variable (est. 30%) | -$4.89 | -$48.90 | -$244.50 | -$489 |
| **Total (30% BYOK)** | **$32** | **$135** | **$637** | **$1,207** |

### Comparison with n8n version

The n8n version has significant fixed costs (n8n Cloud, Apify platform minimum) that dominate at low user counts, plus a per-user variable cost driven primarily by Claude Sonnet's token pricing ($3/$15 per 1M input/output tokens).

**n8n cost breakdown (baseline: 20 companies, 15 jobs/company, daily):**
- Apify platform: $5/month (Starter, as of early 2026 -- prices recently dropped from $39). Scales to $29/month (Scale) or $199/month (Business) at volume. CU pricing: $0.30/CU (Starter), $0.25/CU (Scale)
- Apify compute: ~$5/user/month (browser-based career page scraping, ~0.5 CU/day)
- Claude Sonnet: ~$10/user/month (300 job evaluations x ~$0.033/eval at $3/$15 per 1M input/output tokens)
- n8n Cloud: ~$24/month (Starter). Scales to $50+/month at volume

| Scale | n8n version | Serverless version | Savings |
|---|---|---|---|
| 1 user | ~$44/month | ~$23/month | 2x |
| 10 users | ~$179/month | ~$37/month | 5x |
| 100 users | ~$1,550/month | ~$184/month | **8x** |
| 1,000 users | ~$15,230/month | ~$1,696/month | **9x** |

The savings are driven by the LLM substitution. Claude Sonnet costs ~$0.033 per job evaluation ($3/$15 per 1M tokens). Gemini 2.5 Flash Lite costs ~$0.005 per job evaluation ($0.10/$0.40 per 1M tokens) -- roughly 7x cheaper per call. At 300 evaluations/user/month, that's ~$10/user (Sonnet) vs ~$1.55/user (Flash Lite). This is where the overall savings come from; Apify vs Jina is a rounding error by comparison.

---

## Revenue Model Considerations

### Pricing tiers (suggestion)

At ~$1.63/user/month variable cost (baseline: 20 companies, daily digest), pricing should be accessible enough to convert free users while covering fixed costs quickly. Tier costs scale linearly with company count; free tier also benefits from weekly (not daily) runs.

| Tier | Price | Limits | Your cost | Margin after Stripe |
|---|---|---|---|---|
| Free | $0/month | 5 companies, weekly digest | ~$0.05/user | N/A (acquisition cost) |
| Starter | $5/month | 15 companies, daily digest, custom scoring | ~$1.22/user | ~$3.31 (66%) |
| Pro | $12/month | 50 companies, daily digest, BYOK option, priority email support | ~$4.08/user (less with BYOK) | ~$7.21 (60%) |
| Power | $29/month | 100 companies, daily digest, BYOK, Slack/Discord alerts, API access | ~$8.15/user | ~$19.56 (67%) |

### Stripe fees

Stripe charges **2.9% + $0.30** per transaction for online card payments. No monthly fee. For recurring subscriptions, Stripe Billing adds **0.5%** (Starter plan).

| Tier | Gross | Stripe fee (2.9% + $0.30 + 0.5%) | Net to operator |
|---|---|---|---|
| Starter ($5) | $5.00 | $0.47 | $4.53 |
| Pro ($12) | $12.00 | $0.71 | $11.29 |
| Power ($29) | $29.00 | $1.29 | $27.71 |

### Why $5/$12/$29 instead of $19/$49

- $19/month is a hard sell for a job search tool when LinkedIn Premium is $30 and includes networking, InMail, and insights. The value prop here is narrower -- automated daily digests from specific companies
- $5/month Starter is an impulse purchase. Low friction to convert a free user. At ~$3.31/month net margin, you need ~7 paying Starter users to cover minimum fixed costs
- $12/month Pro is the target tier. BYOK option appeals to technical users and reduces operator costs. Best margin-to-price ratio
- $29/month Power is for active job seekers casting a wide net or serial networkers monitoring many companies. 100 companies at 67% margin is sustainable. This is a personal product -- team/agency use cases are out of scope for now
- Free tier at 5 companies and weekly digest gives enough value to demonstrate the product without excessive cost. At ~$0.05/user/month (5 companies × 4 weekly runs), 1,000 free users cost ~$50/month -- cheap acquisition

### Break-even analysis

| Scenario | Fixed costs | Starter users needed | Pro users needed |
|---|---|---|---|
| Early (Vercel Pro + Supabase/Upstash/Resend free tiers) | $21/month | 7 users | 3 users |
| Growth (Vercel Pro + Supabase Pro + Resend Pro) | $66/month | 20 users | 10 users |

### Revenue projections

Costs below use **tier-mix pricing** (each user's cost based on their plan's company count and frequency), not the uniform baseline.

| Users | Free (80%) | Starter (10%) | Pro (8%) | Power (2%) | Monthly revenue | Monthly cost | Net |
|---|---|---|---|---|---|---|---|
| 100 | 80 | 10 | 8 | 2 | $204 | $86 | **+$118** |
| 500 | 400 | 50 | 40 | 10 | $1,020 | $392 | **+$628** |
| 1,000 | 800 | 100 | 80 | 20 | $2,040 | $717 | **+$1,323** |

The free tier at $0.05/user is nearly free to operate. Margins are healthy even at 100 users because 80% of users cost almost nothing. The business is profitable from the early growth stage -- no need for thousands of users before breaking even.

### Billing integration

Stripe Checkout + Supabase webhooks:
1. User clicks "Upgrade" in dashboard
2. Stripe Checkout session opens (hosted by Stripe -- PCI compliant, no card data touches your servers)
3. On successful payment, Stripe webhook fires
4. Supabase edge function receives webhook, verifies signature, updates `profiles.plan`
5. RLS policies and edge function logic enforce the new limits immediately
6. Stripe handles recurring billing, failed payment retries, invoice emails, and cancellation

---

## UX Gap Analysis

### The current "interface"

There is no user-facing interface. The RoleFinder MVP has no web app, no onboarding flow, no forms, and no dashboard.

The current user experience:

1. Open the n8n admin dashboard (requires n8n instance access)
2. Navigate to the `profiles` Data Table -- a spreadsheet view inside n8n
3. Paste your entire resume into a text cell
4. Type your target criteria as raw JSON into another cell:
   ```json
   {
     "apify_filters": {
       "titleSearch": ["software engineer"],
       "titleExclusionSearch": ["marketing"],
       "timeRange": "7d",
       "locationSearch": ["United States"],
       "aiWorkArrangementFilter": ["Remote OK"]
     },
     "ai_scoring_criteria": {
       "version": "1.0",
       "scoring_model": "weighted_average",
       "dimensions": [
         {"name": "role_alignment", "description": "...", "weight": 0.3},
         {"name": "seniority_fit", "description": "...", "weight": 0.25}
       ]
     }
   }
   ```
5. Add rows to the `companies` Data Table -- manually type each company name and domain
6. Wait for the 7am cron trigger, or manually trigger the Main workflow
7. Receive an email digest

This is the single biggest gap between the MVP and a commercial product. The security issues are invisible to users; the lack of an interface is the first thing they encounter. You cannot charge someone $19/month and ask them to write JSON in a spreadsheet cell.

Issue #7 ("Create Profile Management Workflow") has been open since January 2026 with no progress. Building a UI inside n8n means creating a workflow that serves as a form -- it's fighting the tool's purpose.

### The resume problem

The resume text field serves as the AI's context for personalized scoring. Without it, scoring is reduced to keyword matching ("does the title contain 'software engineer'?"). With it, the AI can make contextual judgments ("this person has 8 years of backend Python experience; this frontend React role is a poor fit despite the generic title").

The concept is sound. The implementation creates serious problems:

**UX problems:**
- A resume is unstructured text. The AI must parse formatting, infer seniority, and guess what matters. Output quality varies based on how the resume is written, not just the candidate's qualifications.
- Resumes are static. People don't update them weekly, but job preferences change frequently. A resume reflects where someone has been, not where they want to go.
- It's one-size-fits-all. A career pivoter's resume actively works against them -- the AI matches on past experience, not stated intent.

**Privacy problems:**
- A resume contains: full name, email, phone number, home address, employment history with dates, education history, and sometimes age, nationality, or photograph.
- In the current pipeline, this data is stored as plaintext in n8n Data Tables, passed through all four workflows via `_context_resume_text`, and sent to the Anthropic API for every job evaluation.
- There is no consent mechanism, no data retention policy, and no deletion flow.

**What should replace it:**

A structured profile that captures intent, not history:

```
Target titles:        ["Senior Backend Engineer", "Staff Engineer"]
Skills to use:        ["Python", "Go", "distributed systems"]
Skills to avoid:      ["PHP", "WordPress"]
Seniority level:      Senior / Staff
Location:             Remote US, or NYC / SF
Compensation floor:   $180k
Deal-breakers:        ["on-site only", "defense/military", "crypto"]
What matters to me:   "small team, high ownership, series B-C"
```

This is easier for the user (form fields instead of document paste), better for the AI (structured input produces more consistent scoring), and carries far less privacy liability (no PII beyond what's functionally necessary).

An optional resume upload can still exist -- as a shortcut to pre-populate the structured profile. Gemini extracts the structured fields, the user reviews and edits, and the raw resume text is discarded. The structured profile is what gets stored and sent to the AI, not the resume.

### GDPR and data protection exposure

If this product serves any EU residents -- or the developer wants the option to operate internationally -- GDPR applies in full. Even a US-focused product can trigger GDPR if EU residents sign up. The current implementation has significant legal exposure.

**What a resume contains under GDPR:**

A resume is personal data under Article 4(1). It typically contains:

- **Identifiers**: Full name, email, phone, address (Article 4(1) -- personal data)
- **Employment history**: Employer names, dates, titles (personal data)
- **Education**: Institutions, dates, qualifications (personal data)
- **Potentially special category data (Article 9)**: Health disclosures, religious affiliations inferred from volunteer work, trade union membership, ethnicity indicators, age/date of birth

Special category data requires **explicit consent** under Article 9(2)(a), with a higher bar than standard personal data processing.

**Current GDPR violations in the MVP:**

| GDPR Requirement | Article | Current state |
|---|---|---|
| **Lawful basis for processing** | Art. 6 | None established. No consent collected, no legitimate interest assessment documented |
| **Explicit consent for special category data** | Art. 9 | No consent mechanism exists |
| **Right to erasure** | Art. 17 | No deletion flow. Resume text persists in n8n Data Tables, email_queue, and Anthropic API logs indefinitely |
| **Right to data portability** | Art. 20 | No export functionality |
| **Records of processing activities** | Art. 30 | No documentation of data flows between n8n, Anthropic, Apify |
| **Data Processing Agreements** | Art. 28 | No DPAs with sub-processors (Anthropic, Apify, n8n Cloud) |
| **Privacy policy** | Art. 13/14 | None exists |
| **Data Protection Impact Assessment** | Art. 35 | Required for AI-based profiling that affects employment. Not conducted |
| **Cross-border transfer safeguards** | Art. 46 | Resume data sent to US-based APIs (Anthropic, Apify) with no Standard Contractual Clauses or adequacy decision documentation |
| **Automated decision-making transparency** | Art. 22 | AI scoring directly influences which job opportunities a person sees. Users have the right to understand the logic, contest decisions, and request human review. None of this is implemented |

**The Article 22 issue is particularly serious.** The product uses automated processing (AI scoring) to evaluate a person's employment prospects. Under GDPR Article 22(1), individuals have the right not to be subject to decisions based solely on automated processing that produce legal or similarly significant effects. Job opportunity filtering qualifies. This requires:

1. Explicit consent to automated decision-making
2. Meaningful information about the logic involved
3. The right to contest the decision and obtain human intervention

**The Article 35 issue is mandatory, not optional.** A Data Protection Impact Assessment is required before processing begins when using new technologies (AI/LLM), processing data at scale, and performing evaluation/scoring of individuals related to employment. This product hits all three triggers.

**Financial exposure:**

- GDPR fines: up to 4% of annual global turnover or EUR 20 million, whichever is higher (Article 83(5))
- For a startup, the realistic risk is a complaint to a Data Protection Authority followed by an enforcement order to cease processing -- effectively shutting down the product in the EU

**How the serverless architecture addresses this:**

| Requirement | Solution |
|---|---|
| Lawful basis | Supabase Auth with explicit consent checkbox at signup |
| Consent for automated decisions | Onboarding flow explains AI scoring, user opts in with granular consent |
| Right to erasure | `DELETE FROM profiles WHERE id = auth.uid()` cascades to all user data via foreign keys. One action, complete deletion |
| Right to portability | Edge function exports all user data as JSON on request |
| Records of processing | Data flow documented: Supabase (controller) -> Jina (processor) -> Gemini (processor) -> Resend (processor) |
| Data Processing Agreements | Supabase, Google (Gemini), Jina, and Resend all offer standard DPAs |
| Privacy policy | Required before launch, template available from Supabase's legal resources |
| DPIA | Must be conducted before launch; structured profile approach reduces scope significantly |
| Cross-border transfers | Supabase offers EU-region hosting. Gemini and Jina API calls still cross borders but are covered by DPAs with SCCs |
| Automated decision transparency | Scoring criteria, dimension weights, and per-job reasoning are stored and visible to the user in the dashboard. Human can override any recommendation |
| Data minimization (Art. 5(1)(c)) | Structured profile collects only what's needed for matching. No full resume stored. This is the strongest GDPR posture |

The single most impactful change is replacing raw resume storage with a structured profile. It satisfies data minimization (Article 5(1)(c)), reduces special category data risk to near zero, and makes right-to-erasure trivial. The resume-as-shortcut approach (upload -> extract structured fields -> discard original) is the recommended pattern.

### US regulatory awareness and mitigation

Most US laws regulating AI in employment (NYC Local Law 144, Illinois HB 3773, Colorado AI Act) target **employers** using AI to screen inbound candidates. RoleFinder is the reverse -- a candidate-facing service that helps job seekers. This is an important distinction and it works in the operator's favor.

That said, it's worth being aware of the evolving landscape so the product is built right from the start. The good news is that the mitigations are straightforward design choices, not expensive legal undertakings.

**What to be aware of:**

- **California CPRA/ADMT regulations** (general regs effective January 1, 2026; ADMT-specific requirements effective January 1, 2027): If any user is a CA resident, best practice is to provide a clear explanation of how the AI scoring works and offer users control over their experience. The ADMT rules require pre-use notice, opt-out mechanisms, and access rights for "significant decisions" -- which includes employment-related processing. Compliance with NIST AI RMF is recommended as it aligns with the Colorado safe harbor (below).

- **Illinois HB 3773** (effective January 1, 2026): Requires notice when AI is used in employment-related decisions. Includes a private right of action. Best practice: be transparent about how scoring works.

- **Colorado AI Act** (effective June 30, 2026): Requires impact assessments and consumer disclosures for "high-risk" AI systems. Importantly, compliance with the **NIST AI Risk Management Framework** provides an explicit safe harbor -- a clear path to legal protection.

- **FTC Section 5**: General authority over unfair or deceptive practices. As long as the product accurately describes what it does and doesn't overclaim, this is low risk.

**The key concern to design for:**

If the AI scoring were to systematically rank opportunities differently for users based on protected characteristics -- even unintentionally -- that's something to get ahead of. For example, if an LLM has embedded biases about which candidates "fit" certain roles, that could subtly affect scoring. The user wouldn't know what they're not seeing.

This isn't a reason to panic -- it's a reason to build transparency into the product from day one. Most of the mitigations below are things you'd want to do anyway for a good user experience.

**Built-in mitigations (mostly just good design):**

| Mitigation | Why it matters |
|---|---|
| **Structured profile with no demographic proxies** | No zip codes, graduation years, school names, or employer names in the scoring prompt. The AI scores based on skills and preferences, not background. This is the single strongest protection |
| **Show all jobs, rank don't filter** | Never hide jobs from the user. Show everything with scores, let them decide. This preserves user agency and makes the tool advisory, not decisional |
| **Transparent scoring with per-job reasoning** | Users can see exactly why each job was scored the way it was. Builds trust and satisfies transparency requirements across all jurisdictions |
| **User feedback mechanism** | "Good match" / "Bad match" buttons help the operator spot issues early if they exist |
| **Terms of service** | Clear language that AI scoring is advisory. The service assists the job search, it doesn't make decisions |
| **Periodic bias testing** (recommended before launch) | Test with synthetic profiles that vary only by demographic proxies. Document that scores are consistent. This satisfies Colorado's impact assessment and is the strongest proactive defense |
| **NIST AI RMF compliance** | Colorado explicitly recognizes this as a safe harbor. Documenting alignment provides legal protection that extends to other jurisdictions |

**Bottom line**: Because this is a candidate-facing tool (not employer-facing), the regulatory risk is lower than for hiring tools. Building with transparency, structured profiles, and a "rank don't filter" approach makes the product both better for users and legally sound. These are design decisions, not legal expenses.

### What the frontend needs

The frontend is not optional -- it's the product. The pipeline is the engine; the frontend is what people pay for. Built with Next.js on Vercel, using Supabase Auth and the Supabase JS client for all data access. Redis (Upstash) caches static/semi-static content to reduce database load and Vercel bandwidth.

**1. Landing / Marketing page**
- Clear value proposition: "Stop manually checking career pages. Get a daily scored digest of new jobs at companies you care about."
- How it works (3 steps): build profile -> add companies -> get daily digests
- Pricing tiers with feature comparison
- Sign up / Log in buttons (Supabase Auth -- email/password or Google OAuth)
- Trust signals: "No resume stored. No PII sent to AI. You control your data."
- Cached via Redis -- near-zero database load, fast global delivery through Vercel's CDN

**2. Onboarding (new user, post-signup)**
- Step 1: Structured profile form
  - Target job titles (tag input, autocomplete from common titles)
  - Skills you want to use (tag input)
  - Skills you want to avoid (tag input)
  - Seniority level (dropdown: Junior / Mid / Senior / Staff / Principal / Director+)
  - Preferred locations (tag input with "Remote" as a first-class option)
  - Work arrangement preference (radio: Remote / Hybrid / On-site / Any)
  - Compensation floor (optional, numeric input)
  - Deal-breakers (tag input, e.g., "defense/military", "crypto")
  - Free-text: "What matters to you in your next role?" (short textarea)
- Step 2: Optional resume upload shortcut
  - "Want to pre-fill from your resume? Upload it and we'll extract the relevant fields. You review and edit before saving. The resume file is not stored."
  - Upload -> Gemini extracts structured fields -> show preview for user to confirm/edit -> discard raw text -> log event to `resume_upload_events`
  - User must explicitly confirm each extracted field before it's saved
- Step 3: Consent
  - Checkbox: "I understand this service uses AI to score job listings against my profile" (required, GDPR Art. 22)
  - Checkbox: "I agree to the Terms of Service and Privacy Policy" (required)
  - Link to privacy policy explaining data flow, sub-processors, and user rights
- Step 4: Add your first company
  - "What company are you interested in?" -- name + career page URL
  - Inline validation: hit Jina Reader to confirm the URL returns job listings
  - "Add more" button, with plan-tier limit displayed ("2 of 5 companies on Free plan")
  - **Pre-built company directory**: During Phase 0 / ramp-up, we build a curated base of known companies with verified career page URLs. New users can browse or search this directory and add companies with one click instead of hunting for URLs. Every user who adds a new company enriches the directory for everyone after them -- a network effect where the product gets better with each signup. Over time this becomes a competitive moat: a crowd-sourced, validated database of career page URLs that no competitor starts with

**3. Dashboard (main view after onboarding)**
- **Today's digest**: scored jobs from the most recent run, sorted by overall score descending
  - Each job card shows: title, company, location, overall score (color-coded), recommendation badge (EXCELLENT / GOOD / CONSIDER / POOR_FIT)
  - All jobs shown, ranked by score. POOR_FIT jobs are collapsed into a "N other jobs" summary at the bottom (expandable) -- rank, don't filter
  - Expand for: score breakdown by dimension, AI reasoning text, link to original posting
  - Action buttons: "Save" (bookmark), "Not interested" (feedback), "View posting" (opens the job on the company's career page in a new tab)
- **Saved jobs**: bookmarked jobs across all digests, filterable by company/score/date
- **Run history**: past digests by date, expandable. Shows stats: companies checked, jobs found, top matches
- **Notification preferences**: email frequency (daily/weekly), digest time, optional Slack/Discord webhook URL (Power tier)

**4. Companies management**
- List of tracked companies with columns: name, career URL, status (active/paused), last checked, jobs found (last run)
- Add company: name + URL, with Jina Reader validation
- Toggle active/paused per company
- Delete company (with confirmation)
- Plan-tier limit indicator: "12 of 15 companies used (Starter plan). Upgrade for more."
- Bulk import (Pro/Power tier): paste a list of company names + URLs

**5. Profile & Settings**
- Edit all structured profile fields (same form as onboarding, pre-filled)
- Scoring criteria customization (Pro tier): adjust dimension weights via sliders, add custom dimensions
- BYOK: "Use your own Google AI API key" -- input field, saved to Supabase Vault, tested on save
- Email preferences: frequency, digest time, format (detailed vs. summary)
- Plan & billing: current plan, usage stats, upgrade/downgrade (Stripe Customer Portal)
- Data & privacy:
  - "Export my data" button (downloads JSON of all profile, company, job, and run data)
  - "Delete my account" button (confirmation dialog, cascading delete, Stripe subscription cancelled)
  - Resume upload history (from `resume_upload_events` -- shows dates, fields extracted, confirmation that raw text was discarded)

**6. Support / Compliance**
- Help page with FAQ (static, cached in Redis)
- Contact form or email link (routes to support@yourdomain via Resend inbound forwarding)
- Privacy policy and Terms of Service pages (static, cached)
- Cookie consent banner (if applicable for EU users)

**Technical notes:**
- All data access through Supabase JS client with RLS -- the frontend never has direct database credentials
- Static pages (landing, help, privacy policy, ToS) cached in Redis with a TTL of 1 hour, served from Vercel's edge. This minimizes bandwidth costs and database load
- Dynamic pages (dashboard, companies, settings) use Supabase Realtime for live updates when a run completes, or fall back to polling
- Responsive design for mobile -- job seekers check email digests on their phone and may want to click through to the dashboard
- Accessible: WCAG 2.1 AA compliance for form inputs, color contrast, screen reader support

---

## What the n8n Version Got Right

Credit where it's due. These design decisions from the original should be preserved:

1. **Profile externalization** - Candidate data in the database, not in code. This is exactly right for multi-tenancy.
2. **Workflow_run_id traceability** - Every job traceable to a specific run. The `runs` table in the proposed schema preserves this.
3. **Fault tolerance per company** - One company failing doesn't kill the whole run. The fan-out edge function pattern improves on this by running companies in parallel.
4. **Dynamic scoring criteria** - User-defined dimensions and weights, not hardcoded. The `scoring_criteria` JSONB column preserves this flexibility.
5. **Recommendation categories** - EXCELLENT_MATCH through POOR_FIT is a good UX pattern. Keep it.

---

## Implementation Phases

### Phase 1: Core pipeline (no frontend)

- Supabase project setup (DB schema with structured profile fields, RLS policies, edge function scaffolding)
- Jina Reader integration in an edge function
- Gemini extraction + scoring in a single edge function (structured profile as AI context, not resume text)
- Email digest builder and sender
- QStash cron scheduling with fan-out per company
- Manual user/company setup via Supabase dashboard or SQL (insert structured profile fields directly). Phase 1 runs with a single test user: one row in `profiles` with a hardcoded UUID, companies added via SQL inserts. No auth -- the edge functions use the test user's ID directly. This proves the pipeline works end-to-end before investing in the frontend
- Deliverable: a working pipeline that sends email digests for the test user, verifiable end-to-end

### Phase 2: User-facing product

- Supabase Auth integration (signup, login, password reset)
- GDPR consent flows and privacy policy
- Frontend dashboard (Vercel + Next.js or static)
  - Onboard flow: structured profile form (target titles, skills, seniority, location, preferences), optional resume upload as pre-fill shortcut (extract -> confirm -> discard raw text)
  - Job history view: past digests, saved jobs, score breakdowns with per-dimension transparency (Article 22 compliance)
  - Company management: add/remove/pause companies
  - Account settings: data export (portability), account deletion (right to erasure)
- Stripe billing integration (free/pro tiers)
- Usage metering and plan limit enforcement
- Data Protection Impact Assessment (DPIA) conducted and documented

### Phase 3: Growth features

- Slack/Discord notifications as an alternative to email
- Company URL auto-discovery ("just type the company name")
- Job alert rules (e.g., "notify me immediately for any EXCELLENT_MATCH")
- Analytics dashboard (match rate trends, most active companies)
- Power user features (advanced scoring customization, API access)

---

## Risks and Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| Career pages block Jina Reader | Jobs not discovered | Monitor for empty results; fall back to direct ATS JSON APIs (Greenhouse, Lever) for known platforms |
| Gemini extraction quality varies | Inconsistent job data | Validate LLM output against a JSON schema; retry malformed responses; log failures for review |
| Supabase Edge Function timeout (150s free / 400s Pro) | Large career pages with 500+ jobs timeout | Paginate: fetch page 1, process, then invoke self for page 2. Pro tier's 400s limit gives more headroom |
| Jina free tier exhausted | Pipeline stops | Monitor usage; paid tier is $0.02/1M tokens (negligible). Cache aggressively (same URL within 24h = skip) |
| GDPR compliance for EU users | Legal liability, enforcement action, product shutdown in EU | Structured profile instead of raw resume (data minimization), DPIA before launch, DPAs with all sub-processors, consent flows, right-to-erasure via cascading deletes, Article 22 compliance via transparent scoring. See UX Gap Analysis -- GDPR section |
| Competitor builds the same thing | Market pressure | Move fast; the scoring criteria customization is the moat, not the infrastructure |

---

## Test Results Appendix

Jina Reader was tested against five platforms on 2026-02-17. All returned usable structured data:

| Platform | Type | Jobs extracted | Fields captured |
|---|---|---|---|
| LinkedIn Jobs | Job aggregator (tested, not a product source) | 10 (login wall limits usefulness) | Title, company, location, filters, top companies |
| Stripe (Greenhouse) | ATS | 100+ | Title, department, location, specialization, language requirements |
| Reddit (custom) | Custom career page | 140+ | Title, department, location, remote status, seniority, contract type |
| Apple (jobs.apple.com) | Proprietary ATS | 20/page (600+ total) | Title, department, location, date posted, role number, URL pattern |
| Xing | European job board | 21 | Title, location, salary range, posting date |

LinkedIn is included for completeness but is not a viable product source due to authentication requirements. The product targets direct company career pages, where Jina Reader performs reliably across all ATS platforms tested.

---

## Summary

The RoleFinder concept is commercially viable. The n8n version proves the idea works. The path from MVP to product requires: multi-tenancy, authentication, data isolation, cost efficiency at scale, and billing. A serverless architecture on Supabase with Jina Reader and Gemini Flash delivers all of these while reducing costs by roughly 8-9x at scale (from ~$1,550/month at 100 users to ~$184/month worst case) and keeping fixed infrastructure costs under $66/month. With realistic tier-mix pricing, the product is profitable at 100 users (+$118/month net).


