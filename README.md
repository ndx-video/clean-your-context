# clean-your-context
A universal prompt for context hygiene in your top-level project folder.

## Copy and paste the prompt below (self-explanatory)

Uses [AGENTS.md](https://agents.md/) as the cross-tool canonical format. Harness-aware for Claude Code, Cursor, Codex, and OpenCode.

---

```
You are an expert in AI harness context engineering. Our project directives (AGENTS.md, CLAUDE.md, .cursor/rules, opencode.json instructions, etc.) have grown bloated, causing context exhaustion and unreliable rule adherence.

**Guiding principle:** Scope is an address, not a sentence. Every rule must have a mechanical load trigger — never prose conditionals like "when working on TypeScript."

**Priority:** Minimize always-loaded tokens. Delete before moving. Move before duplicating.

Scan the repository, then execute:

### 0. Inventory
Discover every directive file. For each: path, line count, load trigger (always / directory-nested / glob / skill / manual), and duplicates across harnesses.

### 1. Delete & deduplicate
- Remove: vague prose, redundant style guides (use linters/formatters), stale file-path references, instructions models already know, contradictions (ask me to resolve).
- Pick AGENTS.md as canonical. Thin adapters only: CLAUDE.md → `@AGENTS.md`; do not maintain parallel copies.
- Migrate enforceable checks to linters, formatters, or hooks — not standing memory.

### 2. Shrink root files (instruction budget)
Root AGENTS.md / CLAUDE.md: ONLY what applies to every task:
- One-sentence project purpose
- Package manager + exact build/test/lint commands
- Definition of done (verifiable exit codes)
- Pointers to deeper docs (not their contents)

Targets: root <60 lines ideal, <150 hard cap. Each scoped rule <50 lines.

### 3. Mechanical scoping
**Directory:** Move local rules to nested AGENTS.md or CLAUDE.md inside the owning package. Codex: AGENTS.override.md to replace parent scope.

**Glob:** Central rules with path patterns — never prose conditions:
- Claude Code: `.claude/rules/*.md` with `paths:` frontmatter
- Cursor: `.cursor/rules/*.mdc` with `globs:` and `alwaysApply: false`
- OpenCode: `opencode.json` `instructions` array with globs

**Procedures:** Extract multi-step workflows to Skills or slash commands — not standing memory.

**Pointers:** Link to topic docs; use file:line refs instead of copied snippets.

### 4. Cross-tool normalization
- Convert legacy `.cursorrules` → scoped `.mdc` rules
- Consolidate scattered `.md` rules into harness-native glob mechanisms
- Monorepos: add claudeMdExcludes for irrelevant ancestor rules
- Human-only notes: HTML comments (stripped from Claude context)

### 5. Verify & report
Produce a table: file, before/after line counts, load trigger, harnesses affected.
List all globs introduced. Report estimated session-start token reduction.
Dry-check: non-global rules must load via nested traversal, glob match, or skill invocation — not session start.
Acid test: build commands must be recitable verbatim from the cleaned root file.

Do not add new rules unless replacing deleted bloat. Present changes before applying if the repo has >10 directive files.
```

## Why this prompt

Most projects do not run out of context because their codebase is too large. They run out because their *instructions* are too large — and too many of those instructions load on every session regardless of the task at hand.

The feedback loop is familiar: the agent makes a mistake, you add a rule, repeat. Over months, `AGENTS.md` or `CLAUDE.md` becomes a ball of mud — contradictory, stale, and full of prose the model treats as optional anyway. Research on instruction-following suggests frontier models can track roughly 150–200 instructions with reasonable consistency, and that performance degrades uniformly as the count rises. Meanwhile, harness system prompts already consume a large share of that budget before your project rules even enter the picture.

The fix is not "write better prose." It is **mechanical scoping**: every rule should have an address that determines when it loads, not a sentence that asks the model to guess. Directory-nested files load when you work in that subtree. Glob-scoped rules load when matching files enter context. Skills and slash commands load on invocation. What remains at the root should be the small set of facts every task needs — project purpose, exact commands, definition of done — not style guides, not multi-step workflows, not file-tree documentation that goes stale the next time someone renames a folder.

Deletion comes first because that is where the real savings are. Style conventions belong in linters and formatters, which are faster, cheaper, and deterministic. Multi-step procedures belong in skills, not standing memory. Duplicate copies across `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, and `.cursor/rules` belong in one canonical file with thin adapters for tool-specific formats. Progressive disclosure — pointers to deeper docs the agent reads on demand — beats copying content into the root.

The prompt is harness-aware because each tool implements scoping differently. Claude Code uses `paths:` frontmatter in `.claude/rules/`; Cursor uses `globs:` in `.mdc` files; OpenCode can consolidate via `opencode.json`; Codex walks a directory chain with a 32 KiB combined cap. The underlying principle is the same across all of them: minimize what loads at session start, scope everything else to an address, and verify that non-global rules actually trigger lazily.

Efficiency is the guiding star. A 40-line root file that the agent follows beats a 400-line root file it ignores.

## Bibliography

Sources consulted when designing this prompt:

- [AGENTS.md](https://agents.md/) — Open standard for agent instructions; nested scoping; cross-tool compatibility
- [Claude Code: How Claude remembers your project](https://code.claude.com/docs/en/memory) — CLAUDE.md loading, `.claude/rules/` path scoping, skills, imports, `claudeMdExcludes`
- [Writing a good CLAUDE.md (HumanLayer)](https://www.humanlayer.dev/blog/writing-a-good-claude-md) — Instruction budget, progressive disclosure, linters over LLM style enforcement
- [AGENTS.md Patterns (Blake Crosley)](https://blakecrosley.com/blog/agents-md-patterns) — Command-first instructions, definition of done, directory scoping, cross-tool compatibility
- [A Complete Guide to AGENTS.md (AI Hero)](https://www.aihero.dev/a-complete-guide-to-agents-md) — Progressive disclosure, monorepo nesting, contradiction resolution
- [How to write a great agents.md (GitHub Blog)](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/) — Lessons from 2,500+ repositories; operational vs. documentary instructions
- [Codex: Custom instructions with AGENTS.md](https://developers.openai.com/codex/guides/agents-md/) — Discovery hierarchy, `AGENTS.override.md`, byte limits
- [Cursor: Rules](https://cursor.com/docs/context/rules) — `.mdc` frontmatter, `globs` / `alwaysApply` / `description`, nested AGENTS.md
- [OpenCode: Rules](https://opencode.ai/docs/rules/) — AGENTS.md precedence, progressive disclosure via `instructions`, Claude Code compatibility
- [OpenCode: Config](https://opencode.ai/docs/config/) — `instructions` array, compaction settings, `.opencode/` directory structure