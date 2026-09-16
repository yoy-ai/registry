# YOY AI - Only Truth AI Model Registry

Production feed for ALL YOY AI installs (HQ + production users).

- File: ai-model-registry.json - providers, models, endpoints, pricing, capabilities, vision flags
- Consumed by: YOY app weekly auto-sync (RegistryClient, 7-day TTL, staggered fetch)
- Chain: this repo (primary) -> mirror -> China mirror -> bundled fallback
- Update cadence: weekly (Monday) by YOY HQ agents, verified against official provider docs

Raw URL consumed by apps:
https://raw.githubusercontent.com/yoy-ai/registry/main/ai-model-registry.json

Data only - no source code lives here.
