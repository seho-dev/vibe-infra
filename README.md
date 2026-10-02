# vibe-infra

> **An Agent-Native, Multi-Source Infrastructure Versioning Specification & Base Repository.**
> 
> **English Documentation** | [中文文档](README.zh-CN.md)
> 
> Unify, distribute, and evolve AI infra configurations across projects through manifest verification, harness-aware placement, flat seamless organization, AI-native semantic merging, and supply-chain security auditing.

Base Repository: `https://github.com/seho-dev/vibe-infra`

---

## Core Specification & Mental Models

All operations and commands in this repository strictly adhere to the standards formulated in **[prompts/shared-concepts.md](prompts/shared-concepts.md)**:

### 1. [Infra Manifest Mandate (`vibe.json`)](prompts/shared-concepts.md#1-infra-manifest-mandate-vibejson)
- **Mandatory Root Manifest**: Every valid Vibe Infrastructure repository **MUST** define a `vibe.json` file at its root specifying its identity and `includes`/`excludes` expressions.
- **Strict Guardrail**: If an upstream repository does **NOT** contain a `vibe.json`, the AI **must reject all operations** and refuse to synchronize. Never guess or copy arbitrary files from non-compliant repositories.
- **Expression-Based Filtering**: Only AI infra configurations matching upstream `includes` and not filtered by `excludes` will be read and merged. Project meta (`README`, `LICENSE`), CI/CD, and dependency locks are strictly blacklisted from being synchronized.

### 2. [Harness-Aware Placement & Asset Agnosticism](prompts/shared-concepts.md#2-harness-aware-placement--asset-agnosticism)
- **Asset Agnosticism**: Vibe Infra does not prescribe, restrict, or exhaustively enumerate the categories of AI configurations an infrastructure can provide. Authors freely distribute assets via `includes` (such as `commands/`, `skills/`, `prompts/`, workflows, or custom domain guidance).
- AI environments utilize distinct native configuration conventions:
  | Harness | Commands Path *(Example)* | Skills Path *(Example)* | Prompts Path *(Example)* | Global Rules Path |
  | :--- | :--- | :--- | :--- | :--- |
  | **Claude Code** | `.claude/commands/` | `.claude/skills/` | `.claude/prompts/` | `AGENTS.md` or `CLAUDE.md` |
  | **Cursor** | `.cursor/rules/` | `.cursor/rules/` | `.cursor/rules/` | `.cursorrules` or `.cursor/rules/` |
  | **OpenCode / Agentic CLI** | `.agents/commands/` | `.agents/skills/` | `.agents/prompts/` | `AGENTS.md` |
  | **Generic / Unknown** | `commands/` | `skills/` | `prompts/` | `AGENTS.md` |
- **Modular Directory Alignment**: Modular asset directories adapt directly to peer locations under the host harness configuration root without synthetic nesting.
- All AI infra configurations must always be adapted and placed into the **native, valid directories of the detected host harness**. Never leave unadapted files scattered in arbitrary root directories.

### 3. [Flat & Seamless Architecture](prompts/shared-concepts.md#3-flat--seamless-organization)
- **Zero Directory Nesting**: Projects maintain flat organization. AI infra configurations are placed directly in their target directories without synthetic nesting (avoid paths like `skills/infra-a/...`).
- **Zero Format Pollution**: Markdown files remain clean and natural. They must **never** contain synthetic machine tags or template comments (such as `<!-- infra-a -->`). Developers can freely edit any file at any time.
- **Homonymous Multi-Source Fusion**: When multiple infrastructures define the same AI infra configuration, the AI merges the principles from each source and local custom rules into a single, cohesive document.
- **Flattened Distribution at Publishing (Infra Dependency Inheritance)**:
  - Infra authors may depend on and extend other infrastructures.
  - Upstream capabilities are pre-fused and flattened into the derivative infra prior to release tagging.
  - Consumer projects maintain a simple **Direct-Only Lock** in `vibe.lock`, eliminating runtime transitive dependency trees and diamond dependency conflicts.
- **Local Intent Always Wins**: Project-specific build commands, architectural boundaries, and business domain logic always override upstream baselines (see [Precedence & Conflict Hierarchy](prompts/shared-concepts.md#4-precedence--conflict-hierarchy)).

### 4. [AI-Native Semantic Provenance & Subtraction](prompts/shared-concepts.md#5-ai-native-semantic-provenance--subtraction)
- Unlike traditional rigid package managers that depend on mechanical file trees and checksums, `vibe.lock` remains ultra-minimalist by recording only the `resolvedCommit`.
- **Semantic Baseline Anchor**: When executing `/vibe-remove`, the AI retrieves the upstream content at `resolvedCommit` as a baseline reference. The AI autonomously identifies equivalent sections and prunes them from fused files (like `AGENTS.md`) without needing synthetic machine comments, and deletes exclusive unmodified files.
- **Diff-Driven Upgrades**: Upgrades (`/vibe-sync`) compare `resolvedCommit` with the target release tag, interpreting commit logs and GitHub release notes for smart semantic merging.

### 5. [Supply Chain Security & Audit Protocol](prompts/shared-concepts.md#6-supply-chain-security--audit-protocol)
- AI infra repositories distribute executable commands and prompts directly into developer agents.
- During `/vibe-add` and `/vibe-sync`, the AI performs mandatory security auditing on upstream diffs: scanning for credential leaks (`.env`, `~/.ssh`), unannounced network outbound commands (`curl`, `wget`), or suspicious system calls.
- Detected anomalies trigger proactive user confirmation before merging.

### 6. [Interactive Inquiries (Ask When Uncertain)](prompts/shared-concepts.md#7-interactive-inquiry-protocol-ask-when-uncertain)
- When facing ambiguous merge resolutions, non-obvious conflicts, missing files, security anomalies, or potential breaking behavioral shifts, the AI must **actively pause and ask the user for confirmation** with clear choices.

### 7. [Mandatory Operation Reporting](prompts/shared-concepts.md#8-standard-operation-report-format)
- Every lifecycle operation (`sync`, `add`, `remove`) must conclude with a structured execution report outlining version changes, harness paths modified, security audit results, and recommended next steps.

---

## Repository Structure

```text
vibe-infra/
├── commands/
│   ├── vibe-sync.md             # Synchronize & upgrade dependencies via Tag diff & flat semantic merge
│   ├── vibe-add.md              # Add new dependency and fuse flatly with security audit
│   └── vibe-remove.md           # Remove dependency with AI-native semantic subtraction
├── prompts/
│   └── shared-concepts.md       # Shared principles: manifest mandate, harness placement, security & inquiry
├── schema.json                  # JSON Schema for vibe.json manifest specification
├── lockfile.schema.json         # JSON Schema for vibe.lock specification
├── vibe.json                    # Manifest declaring this repository as Base Infra with includes/excludes
├── LICENSE
├── README.md                    # English specification (Current document)
└── README.zh-CN.md              # Chinese specification (中文文档)
```

---

## Configuration File Specifications

### 1. Consumer Project Manifest (`vibe.json` in user projects)
Placed at the root of a user's project to declare active infrastructure dependencies:

```json
{
  "$schema": "https://raw.githubusercontent.com/seho-dev/vibe-infra/main/schema.json",
  "infrastructures": {
    "base": {
      "url": "https://github.com/seho-dev/vibe-infra",
      "version": "v1.0.0"
    },
    "team-rules": {
      "url": "https://github.com/example-org/team-vibe-infra.git",
      "version": "v0.2.1",
      "includes": ["skills/**/*.md"], // Optional: selective inclusion
      "excludes": ["skills/legacy-*.md"] // Optional: selective exclusion
    }
  }
}
```

### 2. Upstream Infra Manifest (`vibe.json` in infra repositories)
Placed at the root of an infrastructure repository to define its identity and matching expressions:

```json
{
  "$schema": "https://raw.githubusercontent.com/seho-dev/vibe-infra/main/schema.json",
  "name": "my-go-infra",
  "description": "Enterprise Go engineering standards and AI infra configurations",
  "includes": [
    "AGENTS.md",
    "skills/**/*.md",
    "commands/**/*.md",
    "prompts/**/*.md"
  ],
  "excludes": [
    "README*.md",
    "LICENSE"
  ]
}
```

### 3. Project Lockfile (`vibe.lock`)
Placed at the root of a user's project, automatically maintained by the AI to track resolved commit SHAs:

```json
{
  "$schema": "https://raw.githubusercontent.com/seho-dev/vibe-infra/main/lockfile.schema.json",
  "updatedAt": "2026-10-02T12:00:00Z",
  "infrastructures": {
    "base": {
      "url": "https://github.com/seho-dev/vibe-infra",
      "version": "v1.0.0",
      "resolvedCommit": "a1b2c3d4e5f6..."
    }
  }
}
```

---

## Project Initialization & Installation

To initialize `vibe-infra` in any project without installing external tools, send the following prompt directly to your local AI Agent:

### Onboarding Prompt

```markdown
Please read and follow the specification at https://github.com/seho-dev/vibe-infra to initialize vibe-infra as the Base Infrastructure for this project:

1. Verify that https://github.com/seho-dev/vibe-infra contains a valid root vibe.json manifest.
2. Resolve the latest released Git Tag from https://github.com/seho-dev/vibe-infra (e.g., v1.0.0).
3. Detect the current project's AI host harness and its native configuration paths according to prompts/shared-concepts.md (e.g., Claude Code: .claude/, Cursor: .cursor/, OpenCode: .agents/).
4. Fetch the AI infra configurations matching vibe-infra's includes expressions (commands/, prompts/, etc.) and adapt them into the appropriate native harness directories.
5. Create a `vibe.json` file in the project root declaring the Base Infrastructure pinned to the resolved tag, ensuring the `$schema` field is always included:
   {
     "$schema": "https://raw.githubusercontent.com/seho-dev/vibe-infra/main/schema.json",
     "infrastructures": {
       "base": {
         "url": "https://github.com/seho-dev/vibe-infra",
         "version": "v1.0.0"
       }
     }
   }
6. Generate the initial `vibe.lock` file in the project root recording the resolvedCommit and current timestamp, referencing lockfile.schema.json.
7. Conduct a supply-chain security audit on imported templates and commands.
8. If any naming conflicts, path ambiguities, or questions arise, actively ask for clarification as per prompts/shared-concepts.md.
9. Provide an initialization report summarizing the installed AI infra configurations, configuration paths, security audit outcomes, and next steps.
```

---

## Lifecycle Commands

Once initialized, manage infrastructures directly via the following commands:

### `/vibe-sync`
Synchronize and upgrade all declared infrastructure dependencies.

- **Execution Guard**: Validates that each target repository contains a root `vibe.json`; rejects synchronization if missing.
- **Workflow**:
  1. Imports shared principles from [prompts/shared-concepts.md](prompts/shared-concepts.md).
  2. Compares `vibe.json` target versions against `vibe.lock`.
  3. Inspects `git diff` and commit logs across Git Tags (leveraging Release Notes when available).
  4. Audits incoming diffs for security risks (supply chain security check).
  5. Only synchronizes AI infra configurations matching upstream `includes`/`excludes` expressions.
  6. Executes harness-aware flat semantic merging while strictly preserving local business logic.
  7. Asks the user if any ambiguous conflicts or architectural breaking changes arise.
  8. Updates `vibe.lock` and outputs a comprehensive post-sync report.

### `/vibe-add <git-url>@<tag>`
Add a new infrastructure repository dependency and fuse it into the project.

- **Arguments**: `<git-url>` (repository URL), `@<tag>` (target version, defaults to latest).
- **Execution Guard**: Verifies the repository's root `vibe.json` before performing any operations.
- **Workflow**:
  1. Registers the new dependency in user's `vibe.json` (preserving `$schema`).
  2. Fetches the target version, conducts security audits, and semantically merges matching AI infra configurations into native harness paths based on declared `includes`.
  3. Fuses homonymous configurations into unified Markdown files without directory nesting.
  4. Queries user if conflicting rules or naming collisions occur.
  5. Updates `vibe.lock` and outputs an addition summary report.

### `/vibe-remove <infra-name>`
Safely remove an infrastructure dependency from the project.

- **Arguments**: `<infra-name>` (identifier declared in `vibe.json`).
- **Workflow**:
  1. Locates the `resolvedCommit` for `<infra-name>` in `vibe.lock` to establish the baseline reference.
  2. Performs **AI-native semantic subtraction**: removes exclusive unmodified files, and cleanly prunes rules with equivalent meaning from shared files (`AGENTS.md`) without synthetic tag pollution.
  3. Prompts the user if locally customized configurations are detected before removing them.
  4. Unregisters the entry from `vibe.json` and `vibe.lock`.
  5. Outputs a removal report detailing deleted files and preserved configurations.

---

## Authoring Custom Infra Repositories

To author and distribute an infrastructure repository that conforms to the Vibe specification:

1. **Root Manifest**: You **MUST** place a `vibe.json` file in your repository root defining your `includes` and `excludes` (referencing `$schema`):
   ```json
   {
     "$schema": "https://raw.githubusercontent.com/seho-dev/vibe-infra/main/schema.json",
     "name": "my-team-infra",
     "includes": [
       "AGENTS.md",
       "skills/**/*.md",
       "commands/**/*.md"
     ],
     "excludes": [
       "README*.md",
       "LICENSE"
     ]
   }
   ```
2. **Infra Dependency Inheritance**: If your infra builds on top of another infra (e.g. `base`), declare it in your `vibe.json` and pre-fuse upstream rules into your repo via `/vibe-sync` before releasing. Downstream projects will consume your infra directly.
3. **Standard Git Tags**: Mark versions using Git Tags and push them upstream:
   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```
4. **GitHub Releases (Optional)**: Creating a GitHub Release with release notes helps the AI better interpret your intent during downstream semantic merges.
