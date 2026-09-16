# YOY AI - Only Truth AI Model Registry

Production feed for ALL YOY AI installs (HQ + production users).

- File: ai-model-registry.json - providers, models, endpoints, pricing, capabilities, vision flags
- Consumed by: YOY app weekly auto-sync (RegistryClient, 7-day TTL, staggered fetch)
- Chain: **this repo (official) -> China mirror (nindeai.cn) -> bundled fallback**
- Update cadence: weekly (Monday) by YOY HQ agents, verified vs official provider docs

## MANDATORY SCHEMA - READ BEFORE EDITING

Schema v1 is LOCKED. Never hand-edit this JSON.
- Spec + update playbook: src/ai/REGISTRY-SCHEMA.md (source repo, HQ agents only)
- Validator (ships to production): src/ai/registry-schema.js
- Production apps never read the spec doc - only the JSON + validator. Zero token cost.

Rules every HQ agent MUST follow:
1. Locked vocabulary: categories, chatRole, costTier, costUnit, riskFlags.
2. Every visible model MUST declare pricing provenance (costIn/costOut/costTier/costUnit).
   Unverified prices => costUnit "unknown" + unverified flag. NEVER invent a price.
3. The emoji attribute is DERIVED by rules - never hand-written.
4. After editing: `npm run registry:validate && npm run registry:sync` (syncs all runtime
   copies + rebuilds cache), `npm run registry:publish`, then verify the chain.
5. Publishing gate: an invalid registry is rejected and production keeps the last good version.

Raw URL consumed by apps:
https://raw.githubusercontent.com/yoy-ai/registry/main/ai-model-registry.json

Data only - no source code lives here.

## China mirror (mandatory - not optional)

Chinese installs cannot reach GitHub, so they read the registry from
`https://nindeai.cn/registry/ai-model-registry.json` (Alibaba Cloud OSS bucket
behind the China brand 您的AI). **If the mirror is stale, every CN user silently
stays on an old registry** - there is no error, no retry, no warning.

- Automation: the `Sync China mirror` Action in this repo uploads the JSON to the
  OSS bucket on every push to `main`.
- It needs these repo secrets (Settings -> Secrets and variables -> Actions):
  | Secret | Value |
  | --- | --- |
  | `ALIYUN_OSS_ACCESS_KEY_ID` | RAM user with write access to the bucket |
  | `ALIYUN_OSS_ACCESS_KEY_SECRET` | matching secret |
  | `ALIYUN_OSS_BUCKET` | e.g. `nindeai-site` |
  | `ALIYUN_OSS_ENDPOINT` | e.g. `oss-cn-hongkong.aliyuncs.com` |
- Until the secrets exist the job **skips with a warning** (it does not fail the
  build) and the mirror must be updated by hand - run
  `npm run registry:sync-cn` from the HQ source repo with the same env vars.
- Always finish with `npm run registry:check-feeds`; it must report every source
  as OK. A stale mirror is a defect.

### App-update mirror (same bucket)

`https://nindeai.cn/updates/latest-mac.yml` / `latest.yml` + installers serve the
China app-update feed (dual-updater, generic provider). If those return 404, CN
users cannot update the app at all. Upload them with:

```
node scripts/sync-cn-mirror.js --file out/latest-mac.yml --key updates/latest-mac.yml
node scripts/sync-cn-mirror.js --file out/<installer>.dmg --key updates/<installer>.dmg --no-verify
```
