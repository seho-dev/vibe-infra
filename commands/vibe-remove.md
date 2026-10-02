---
description: Remove an AI infrastructure repository dependency from the project, perform semantic subtraction, and clean up exclusive assets.
globs: vibe.json, vibe.lock
---

# vibe-remove: Remove Infrastructure Dependency

> **Core Standards & Mental Models**: This command strictly imports and adheres to [prompts/shared-concepts.md](../prompts/shared-concepts.md) (or the installed `shared-concepts` in the local harness skills directory). Review that document for Harness-Aware Placement, Flat Organization, Precedence Rules, and Inquiry Protocols.

You are the Agent responsible for safely removing an AI infrastructure repository dependency, performing clean semantic subtraction, and removing orphan AI infra configurations while protecting local project customizations.

---

## Usage

Invoked by the user as:
- `/vibe-remove <infra-name>`
- Or conversational prompt: `"Remove company-infra from the project"`

---

## Execution Workflow

### Step 1: Validate Dependency State
1. Read `vibe.json` and `vibe.lock`.
2. Verify `<infra-name>` is registered. If not found, list available dependencies and request clarification.
3. Retrieve `<infra-name>`'s `resolvedCommit` from `vibe.lock` to establish the **Semantic Baseline Anchor**.

### Step 2: AI-Native Semantic Subtraction & Asset Cleanup
Leverage the upstream baseline at `resolvedCommit` as the semantic reference. Let AI autonomously analyze semantic equivalence and ownership:

1. **Exclusive, Unmodified Files**:
   - Inspect files associated with `<infra-name>`'s manifest `includes`.
   - If a file's semantic content matches upstream baseline and carries **zero local customizations or foreign extensions**, delete it safely.
2. **Locally Customized Files**:
   - If an exclusive file contains local edits or domain adaptations, do NOT delete it silently. Ask the user whether to preserve it as an unmanaged local project configuration.
3. **Shared / Fused Files (`AGENTS.md`, `.cursorrules`, merged skills)**:
   - Identify rule clauses that share equivalent meaning with the target infrastructure baseline.
   - Cleanly prune those equivalent sections while strictly preserving:
     - All project-specific business rules and build commands.
     - Rules contributed by remaining active infrastructures.
     - Custom user adaptations.
   - Rebalance Markdown structure and maintain clean narrative flow.

### Step 3: Proactive User Inquiry (When Uncertain)
Follow the inquiry protocol in `shared-concepts.md`. Prompt the user if:
- Heavily modified configurations obscure the boundary between infra baselines and local extensions.
- Removal of a configuration poses potential disruption to local workflows.

### Step 4: Update Manifest & Lockfile
1. Remove target entry from `vibe.json`.
2. Remove corresponding entry from `vibe.lock`.

### Step 5: Post-Remove Execution Report
Emit the standard report as defined in `shared-concepts.md` detailing the removed infrastructure, pruned configurations, preserved local items, and behavioral impacts.
