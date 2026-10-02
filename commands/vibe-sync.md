---
description: Synchronize and upgrade all declared infra repositories according to vibe.json and vibe.lock using flat semantic merging based on GitHub Tag/Commit Diffs.
globs: vibe.json, vibe.lock
---

# vibe-sync: Synchronize & Upgrade Infrastructures

> **Core Standards & Mental Models**: This command strictly imports and adheres to [prompts/shared-concepts.md](../prompts/shared-concepts.md) (or the installed `shared-concepts` in the local harness directory). Review that document for Harness-Aware Placement, Flat Organization, Precedence Rules, and Inquiry Protocols.

You are the Agent responsible for synchronizing, upgrading, and semantically merging the AI infra configurations of the current project.

---

## Execution Workflow

### Step 1: Detect Harness & Read Configuration
1. Detect host harness according to `shared-concepts.md` (e.g., Claude Code, Cursor, OpenCode).
2. Read `vibe.json` and `vibe.lock` at the project root.
   - If `vibe.json` is missing, halt and instruct the user to run `/vibe-add` or initialize `vibe-infra`.
   - If `vibe.lock` is missing, treat all entries as initial sync targets.

### Step 2: Validate Upstream Manifests, Fetch Diffs & Security Audit
For each declared infrastructure entry in `vibe.json`:
1. **Mandatory Guardrail**: Ensure upstream repository contains a root `vibe.json`. If missing, reject and report an error.
2. Compare target `version` (Git Tag / Branch) with `resolvedCommit` recorded in `vibe.lock`.
3. Fetch the target version content and diffs:
   - If an update is detected (e.g., `v1.0.0` -> `v1.1.0`), inspect the `git diff <old-commit>..<new-tag>` and commit logs.
   - If GitHub Release notes exist, read them as semantic guidance to understand author intent, deprecations, and architectural changes.
4. **Supply Chain Security Audit**:
   - Audit diffs for high-risk modifications (e.g., unauthorized network requests, credential leaks, invasive file modifications, or dangerous shell commands).
   - If unexpected security implications are found, immediately halt and prompt the user.

### Step 3: Flat Semantic Merge & Local Adaptation
1. **Filter Files via Includes / Excludes**:
   - Only synchronize AI infra configurations matching upstream `includes` expressions and not excluded by `excludes` (or user's per-dependency filters).
   - Modular asset directories (e.g., `commands/`, `skills/`, `prompts/`, etc.) align to their respective peer paths directly under the host harness folder.
2. **Single-Instance AI Infra Configurations (`AGENTS.md`, `.cursorrules`)**:
   - Parse changes between upstream baseline versions.
   - Incrementally integrate upstream behavioral updates, mental models, and prompt enhancements.
   - **Local Priority**: Ensure all local build commands, testing setups, domain constraints, and custom AI infra configurations are completely preserved.
3. **Modular AI Infra Configurations (`skills/*.md`, `commands/*.md`, etc.)**:
   - **Homonymous Fusion**: If multiple infras (or an infra and local configuration) define the same AI infra configuration, synthesize principles into a single unified Markdown file.
   - **New Configurations**: Place newly added configurations flatly into the harness's native directory.
   - **Deprecations / Removals**: If an upstream configuration was removed:
     - If the local file matches the old upstream baseline and has no local extensions, delete it.
     - If the local file has custom modifications, keep the file and ask the user how to handle it.
4. **Dynamic Environment Adaptation**:
   - Inspect local project descriptors (e.g., `go.mod`, `package.json`, `Cargo.toml`).
   - Adapt upstream generic placeholders into native project commands (e.g., `go test ./...` or `pnpm test`).

### Step 4: Proactive User Inquiry (When Uncertain)
Apply the inquiry protocol defined in `shared-concepts.md`. You must stop and ask the user if:
- **Security Audit Warnings**: Detected potentially dangerous commands or external data transfer.
- **Semantic Conflicts**: Upstream introduces an AI infra configuration contradicting an existing local pattern or another infra.
- **Ambiguous Deletions**: An upstream AI infra configuration was removed or drastically rewritten, but local edits exist.
- **Architectural Impact**: A change in configurations might alter coding conventions or test assertions.

### Step 5: Update `vibe.lock`
Record the newly resolved commit SHAs, URLs, and timestamps in `vibe.lock`:
```json
{
  "$schema": "https://raw.githubusercontent.com/seho-dev/vibe-infra/main/lockfile.schema.json",
  "updatedAt": "<ISO-8601-Timestamp>",
  "infrastructures": {
    "<infra-name>": {
      "url": "<git-url>",
      "version": "<target-tag>",
      "resolvedCommit": "<commit-sha>"
    }
  }
}
```

### Step 6: Post-Sync Execution Report
Emit the standard report matching the format in `shared-concepts.md` detailing version transitions, modified harness paths, security audit results, inquiry outcomes, and potential impacts.
