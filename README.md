# YOY AI - Only Truth AI Model Registry

Production feed for ALL YOY AI installs (HQ + production users).

- File: ai-model-registry.json - providers, models, endpoints, pricing, capabilities, vision flags
- Consumed by: YOY app weekly auto-sync (RegistryClient, 7-day TTL, staggered fetch)
- Chain: this repo (primary) -> mirror -> China mirror -> bundled fallback
- Update cadence: weekly (Monday) by YOY HQ agents, verified vs official provider docs

## MANDATORY SCHEMA - READ BEFORE EDITING

Schema v1 is LOCKED. Never hand-edit this JSON.
- Spec + 7-step update playbook: src/ai/REGISTRY-SCHEMA.md (source repo, HQ agents only)
- Validator (ships to production): src/ai/registry-schema.js
- Production apps never read the spec doc - only the JSON + validator. Zero token cost.

Rules every HQ agent MUST follow:
1. Locked vocabulary: categories, chatRole, costTier, costUnit, riskFlags.
2. Every visible model MUST declare pricing provenance (costIn/costOut/costTier/costUnit).
   Unverified prices => costUnit "unknown" + unverified flag. NEVER invent a price.
3. The emoji attribute is DERIVED by rules - never hand-written.
4. After editing: npm run registry:validate && npm run registry:sync (syncs all runtime copies + rebuilds cache), then push here.
5. Publishing gate: an invalid registry is rejected and production keeps the last good version.

Raw URL consumed by apps:
https://raw.githubusercontent.com/yoy-ai/registry/main/ai-model-registry.json

Data only - no source code lives here.
