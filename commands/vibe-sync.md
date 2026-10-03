---
description: Synchronize and upgrade declared infra dependencies via sequential multi-version release traversal and flat semantic merging.
globs: vibe.json, vibe.lock
---

# vibe-sync: Synchronize & Upgrade Infrastructures

> **Normative Reference**: Strictly implements [prompts/shared-concepts.md](../prompts/shared-concepts.md).

```mermaid
flowchart LR
    A["1. State & Harness"] --> B["2. Tagged READMEs"]
    B --> C["3. Release Traversal & Audit"]
    C --> D["4. Flat Semantic Merge"]
    D --> E["5. Lock & Report"]
```

---

## Execution Pipeline

### Stage 1: State & Harness Resolution
1. Detect host harness (such as Claude Code or other mainstream agentic tools). If ambiguous, prompt the user to specify their AI tool as required by `shared-concepts.md`.
2. Read project root `vibe.json` and `vibe.lock`.
   - If `vibe.json` is missing: Halt and prompt user to initialize `vibe-infra`.
   - If `vibe.lock` is missing: Treat all entries as initial sync targets.
3. Determine workspace mode from `vibe.json` `role`:
   - **Consumer Mode (`role: "consumer"`)**: Targeting native harness directories.
   - **Provider Mode (`role: "provider"`)**: Commands in the harness read and modify outer root assets directly.

### Stage 2: Mandatory Cognitive Grounding
1. **Base Infra README Grounding**: Fetch and read the Base Infra repository's (`seho-dev/vibe-infra`) `README.md` at its declared tag (or resolved latest tag) in `vibe.json` into working context.
2. **Target Infra README Grounding**: For each declared entry in `vibe.json`, resolve its target Git Tag (the latest released stable tag when declared as `"latest"`, or the declared pinned tag) and fetch and read its `README.md` to ground domain understanding (Note: Read for context only; never copy into workspace).

### Stage 3: Multi-Version Traversal, Audit & Flat Semantic Merge
For each declared infrastructure entry:
1. **Manifest Guardrail**: Verify upstream repository contains root `vibe.json`. If missing, reject and report error.
2. **Target Resolution & Version Comparison**:
   - Determine `targetVersion`:
     - If declared `version` is `"latest"`, resolve upstream repository's latest released stable Git Tag.
     - If declared `version` is a pinned tag, use that tag.
   - Compare `targetVersion` with the locked `version` and `resolvedCommit` in `vibe.lock`.
   - If already up-to-date with identical commit, skip to the next dependency.
3. **Sequential Multi-Version Release Traversal**:
   - If upgrading across multiple tags (from current locked tag to `targetVersion`):
     1. Discover all intermediate Git Tags in `(currentLockedVersion, targetVersion]`.
     2. Sequentially read GitHub Release Notes and Changelog entries in chronological order.
     3. Extract intermediate deprecations, breaking changes, and evolutionary rationale.
4. **Supply Chain Security Audit**: Inspect cumulative diff for sensitive credential access, suspicious shell commands, or silent outbound calls. Halt and trigger Interactive Inquiry if detected.
5. **Flat Semantic Merge**:
   - Filter files matching upstream `includes` and not excluded by `excludes`.
   - **Single-Instance Rules (`AGENTS.md`)**: Three-way semantic merge; **local business intent and build commands always win**.
   - **Modular Assets (`skills/`, `commands/`, `prompts/`)**: In consumer mode, place into native harness paths. In provider mode, update root flat assets directly. Homonymous files are synthesized into unified documents.
   - **Deprecations**: Delete untouched obsolete files; prompt user if local customizations exist.
6. **Dynamic Stack Adaptation**: Resolve placeholders to native project toolchain (e.g. `go test ./...`, `pnpm test`).

### Stage 4: Atomic Lock & Execution Report
1. Update `vibe.lock` recording resolved concrete Git Tag in `version`, exact `resolvedCommit` SHAs, URLs, and current timestamp.
2. Emit the standard execution report matching the schema in `shared-concepts.md`.
