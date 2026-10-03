---
description: Add a new AI infrastructure repository dependency, ground domain context from tagged READMEs, and perform flat semantic fusion.
globs: vibe.json, vibe.lock
---

# vibe-add: Add Infrastructure Dependency

> **Normative Reference**: Strictly implements [prompts/shared-concepts.md](../prompts/shared-concepts.md).

```mermaid
flowchart LR
    A["1. Parse & Manifest"] --> B["2. Tagged READMEs"]
    B --> C["3. Audit & Flat Fusion"]
    C --> D["4. Lock & Report"]
```

---

## Execution Pipeline

### Stage 1: Parse Arguments & Register Manifest
1. Parse user input: `<git-url>` and optional target `<tag>`.
2. **Manifest Versioning Policy**:
   - **Default (`latest`)**: Unless the user explicitly specifies a pinned tag (e.g., `<url>@<tag>`), set `"version": "latest"` in `vibe.json`. This expresses tracking intent, enabling subsequent `/vibe-sync` commands to automatically detect upstream upgrades.
   - **Explicit Pinning**: If a specific tag is passed, record that pinned `<tag>` in `vibe.json`.
3. If `vibe.json` does not exist, initialize it ensuring `role` is defined (`"consumer"` for engineering projects, `"provider"` for infra author repositories).
4. Register or update the dependency entry under `infrastructures` in root `vibe.json` (always preserving `$schema`).

### Stage 2: Manifest Guardrail & Cognitive Grounding
1. **Manifest Guardrail**: Verify target upstream contains a root `vibe.json`. If missing, abort immediately.
2. **Tag Resolution**:
   - If target version is `"latest"`, query upstream repository to resolve the latest released stable Git Tag.
   - If target version is explicitly pinned, use that specific Git Tag.
3. **Cognitive Grounding (Tagged README Mandate)**:
   - Fetch and read the Base Infra repository's (`seho-dev/vibe-infra`) `README.md` at its declared tag (or resolved latest tag) to ground operational mental models.
   - Fetch and read the target repository's `README.md` at the resolved Git Tag to ground domain understanding (e.g. Go standards, frontend design rules).
   *(Note: Tagged READMEs are read into working memory only; never copied to workspace).*
4. Detect host harness environment (e.g. Claude Code or other mainstream agentic tools). If ambiguous, prompt the user to specify their AI tool as required by `shared-concepts.md`.
5. Determine workspace mode from `vibe.json` `role`:
   - **Consumer Mode (`role: "consumer"`)**: Targeting native harness directories.
   - **Provider Mode (`role: "provider"`)**: Updating outer root assets directly.

### Stage 3: Security Audit & Flat Semantic Fusion
1. **Supply Chain Security Audit**: Inspect incoming configurations and prompt templates for secret leaks, command injection, or unauthorized network calls. Trigger Interactive Inquiry if detected.
2. **Flat Semantic Fusion**:
   - Filter files matching declared `includes` and not excluded by `excludes`.
   - **Single-Instance Rules (`AGENTS.md`)**: Semantically append and synthesize new guidelines; local domain logic strictly takes precedence.
   - **Modular Assets (`skills/`, `commands/`, `prompts/`)**: Map to native harness paths in consumer mode, or edit outer root assets in provider mode without folder nesting. Homonymous files are synthesized with local patterns.
   - **Tooling Adaptation**: Map generic placeholders to actual project commands (`package.json`, `go.mod`).

### Stage 4: Atomic Lock & Execution Report
1. Record the resolved concrete Git Tag in `version` and the resolved commit SHA in `resolvedCommit` into `vibe.lock`.
2. Output standard execution report matching the schema in `shared-concepts.md`, clarifying manifest intent (`latest` or pinned tag) versus locked resolution.
