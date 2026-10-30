# Design decisions

Short records of why things are the way they are. These are interview talking points.

## 001: One generic platform, two domains

**Decision:** Build ingestion, storage, tiles and analysis without domain-specific code; F1 and earthquake response are modules on top.
**Alternatives:** Two separate apps.
**Why:** A multi-sector mapping company needs the same pattern. Adding a new domain should mean new source adapters and a new module, not changes to the core.

## 002: Local first, S3 API from day one

**Decision:** Develop locally with an S3-compatible server (SeaweedFS); all storage goes through boto3; AWS only at the deploy phase.
**Alternatives:** Develop directly on AWS; use local file paths.
**Why:** $0 budget and faster iteration. Using the S3 API from the start makes moving to AWS a configuration change.

## 003: Start as a monolith, extract services with a reason

**Decision:** Begin with one Django project; extract the dataset, telemetry and analysis services when the roadmap reaches them.
**Alternatives:** Microservices from day one.
**Why:** Each split should be justified (heavy dependencies, long-running work, independent scaling, external failure modes). Learning when to split is the actual skill.
