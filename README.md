# Ayse Ozdincer

Software engineer in Los Angeles. I build ML-backed products end to end: generation and data pipelines, model evaluation, cloud infrastructure, and the product on top. One of them is in production with brand clients and the other is pre-launch.

---

## Bernardo: AI imagery production platform
**Sole engineer · private repo · in production for [Daphni](https://daphni.ai)**

Bernardo runs production for Daphni, an AI imagery studio for fashion brands. Brands upload garment photos, and the platform turns them into e-commerce imagery that moves through generation, retouching, QA and client approval. 

**What's in the repo**

- **Multi-tenant web app (Next.js 16, React 19, TypeScript).** Four portals for brand clients, the internal team, retouchers and external artists. Tenancy comes from the database, and every visibility rule is enforced on the server and documented as a state model per audience.
- **Generation pipeline.** Image-generation model → background removal → exact-color fill → S3. Every render gets its own key and keeps its recipe, so any version can be regenerated exactly or traced back to its inputs.
- **Review workflow.** Versioned shots per slot, comments and markups, approvals, retouch jobs with a full lifecycle, and client feedback that queues itself for regeneration.
- **Agent loop (built, rolling out).** Plans crops and references, renders, has a vision model check the result and retries up to three times. It also turns reviewer and client comments into new render plans.
- **Evaluation research.** An offline harness rebuilds the exact inputs of every past render from CloudWatch logs, S3 and the database, then replays production through a planner/judge loop. The vision-model QA checker it produced, scored against human labels, caught 57 of 58 rejected renders at about 1.5¢ per render.
- **Infrastructure (AWS).**
  - Docker on ECS, with a scripted deploy and a rollback alarm.
  - Private RDS Postgres through Drizzle ORM. It opens only for scripted maintenance jobs, which report by default and write only with an explicit flag.
  - Direct-to-S3 presigned uploads, with HEIC conversion and thumbnails done in the browser.
  - CloudFront signed cookies, so only authorized users can load images.
  - Clerk auth backed by a database allowlist.
- **Performance.** Streaming zip exports cut server memory on a 110 MB download from 234 MB to under 31 MB, and memory no longer grows with archive size.

---

## d-mia: AI personal stylist
**Founder and sole engineer · private repo · [d-mia.com](https://www.d-mia.com)**

An AI personal stylist that learns each shopper's taste and builds outfits around it. I designed and built everything in the repo: backend, infrastructure, data pipelines, model evaluation and the site.

**What's in the repo**

- **Event ingestion (Node.js, AWS Lambda).** Webhook service for a third-party messaging platform: HMAC-SHA256 signature verification with constant-time comparison, the subscription handshake, and loop protection so the bot never replies to itself.
- **Waitlist API (DynamoDB).** Idempotent signups through conditional writes, an origin allowlist for CORS, and honeypot spam filtering.
- **Security and deploys (IAM, GitHub Actions).** A least-privilege execution role scoped to a single table, and keyless deploys through GitHub OIDC (no long-lived AWS credentials in CI) that run the test suite before shipping.
- **Catalog pipeline (Python).** Pulls products from an affiliate-network API under a hard request budget.
  - Normalizes and validates records, drops impossible prices, and assigns price tiers by per-category percentile.
  - Rebuilds from saved raw snapshots at zero API cost.
  - Runs locally, on EC2, or as a GitHub Actions job that publishes each run as a release.
- **Model evaluation harness (Python).** Benchmarks several vision-language models on structured extraction from product photos against a strict JSON schema, made portable across providers' strict structured-output modes.
  - Reports schema validity, cross-model agreement, accuracy spot-checks against retailer-provided attributes, field informativeness, latency, and cost per 1,000 items.
  - Sends requests only to providers that don't retain or train on the data, with hard spend caps, rate-limit-aware pacing, and resumable runs.
- **Data model (Postgres + pgvector, design).** Content deduplicated by perceptual hash; user actions stored as events that point at shared content; every derived row tagged with the model, prompt and schema version that produced it, so any step can be rerun without re-fetching. User deletion and media-retention rules are part of the design from day one.
- **Site (HTML/CSS, AWS Amplify).** Hand-built static site with self-hosted fonts and no build step, deployed on every push.


---

<!-- Contact: add your LinkedIn and/or email here -->
