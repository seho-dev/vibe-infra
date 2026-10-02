---
description: Add a new AI infrastructure repository dependency to the project and perform harness-aware flat semantic merging.
globs: vibe.json, vibe.lock
---

# vibe-add: Add Infrastructure Dependency

> **Core Standards & Mental Models**: This command strictly imports and adheres to [prompts/shared-concepts.md](../prompts/shared-concepts.md) (or the installed `shared-concepts` in the local harness directory). Review that document for Harness-Aware Placement, Flat Organization, Precedence Rules, and Inquiry Protocols.

You are the Agent responsible for introducing and semantically integrating new AI infra configurations into the current project.

---

## Usage

Invoked by the user as:
- `/vibe-add <git-url>@<tag>`
- Or conversational prompt: `"Add infrastructure repository https://github.com/foo/bar.git with tag v1.0.0"`

---

## Execution Workflow

### Step 1: Parse Arguments & Initialize Manifest
1. Extract `<git-url>` and target `<tag>` (default to default branch or latest tag if omitted).
2. Derive a concise alias for the infrastructure.
3. Update or create `vibe.json` at the project root (always include `$schema`):
   ```json
   {
     "$schema": "https://raw.githubusercontent.com/seho-dev/vibe-infra/main/schema.json",
     "infrastructures": {
       "<infra-name>": {
         "url": "<git-url>",
         "version": "<tag>"
       }
     }
   }
   ```

### Step 2: Validate Upstream Manifest & Detect Harness
1. **Mandatory Guardrail**: Verify that upstream repository contains a root `vibe.json`. If missing, abort immediately.
2. Detect host harness and resolve canonical paths according to `shared-concepts.md` (e.g., Claude Code, Cursor, OpenCode).

### Step 3: Fetch Upstream, Security Audit & Flat Semantic Integration
1. **Filter Files via Includes / Excludes**:
   - Filter files from the upstream repository based on its `includes` and `excludes` definitions.
   - Modular asset directories (e.g., `commands/`, `skills/`, `prompts/`, or custom asset directories) adapt to their respective peer paths under the designated host harness directory.
2. **Supply Chain Security Audit**:
   - Inspect upstream configurations and prompt templates for high-risk operations (e.g., credential access like `.env`/`.ssh`, arbitrary bash execution, or unannounced external network requests).
   - If suspicious patterns are detected, alert the user and trigger an interactive inquiry before integration.
3. **Single-Instance Configurations (`AGENTS.md`, `.cursorrules`)**:
   - Semantically append and synthesize new guidelines into local rule files.
   - **Local Priority**: Preserve all existing local business context, build commands, and constraints.
4. **Modular Configurations (`skills/*.md`, `commands/*.md`, etc.)**:
   - Place flatly into native harness directories without folder nesting.
   - **Collision Handling**: If an AI infra configuration with the same name already exists locally, fuse generic baselines or consult the user if conflicting local customizations exist.
5. **Dynamic Stack Adaptation**:
   - Inspect project identifiers (`go.mod`, `package.json`, etc.) and adapt placeholders to match native project tooling.

### Step 4: Proactive User Inquiry (When Uncertain)
Apply the inquiry protocol in `shared-concepts.md`. Prompt the user if:
- Security audit reveals sensitive external calls or suspicious commands.
- Naming collisions occur with incompatible intents.
- Direct rule contradictions arise.
- Target harness directories are ambiguous.

### Step 5: Update `vibe.lock`
Record the newly added infrastructure details in `vibe.lock`:
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

### Step 6: Post-Add Execution Report
Emit the standard report as defined in `shared-concepts.md` outlining the newly added infrastructure, installed AI infra configurations, security audit findings, inquiries resolved, and potential behavioral impacts.
