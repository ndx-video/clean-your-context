# clean-your-context
A universal prompt for context hygene in your top-level project folder.

## Copy and paste the prompt below (self-explanatory)

---

```
You are an expert software architect specializing in optimizing AI context windows and project-level rule architectures. Our project configuration files/directives (e.g., AGENTS.md, .cursor/rules, or custom memory files) have grown bloated, leading to context exhaustion and unreliable rule adherence. 

Your task is to refactor our codebase directives using proper mechanical boundaries ("scope is an address, not a sentence") rather than relying on global prose guidance.

Please scan our root configuration files and subdirectories, then execute the following refactoring strategy:

### 1. Identify Bloat & Move Local Rules (Per-Directory Boundary)
- Find any rules currently sitting in global/root memory files that explicitly or logically only matter for a specific subdirectory, package, or component.
- Move these rules out of the root file and create/append them to a dedicated project memory file located directly INSIDE that specific subdirectory. 
- Ensure that the team/folder ownership boundaries are preserved so that these instructions are only pulled into context lazily via nested traversal when working inside that directory.

### 2. Centralize Scattered Patterns (Path-Glob Scoping)
- For rules that must remain centrally managed at the root, or rules that apply to specific file extensions scattered across multiple folders, do NOT use prose to restrict them (e.g., do not write "This rule applies only to TypeScript files").
- Instead, place these rules under the appropriate central rules configuration block (such as `.claude/rules/`) and explicitly define a `paths` field using an accurate glob pattern (e.g., `app/**/*.ts`) to make the rule strictly conditional.

### 3. Extract Multi-Step Procedures (Skill Scoping)
- Audit our directives for any standing instructions that are actually multi-step procedures, checklists, or interactive workflows rather than static conventions.
- Extract these procedures completely out of standing memory files and convert them into standalone "Skills". 
- Add the corresponding `paths` glob pattern to the front matter of these skills so they are conditionally loaded only when working with matching files.

### 4. Verification Check
- After moving and restructuring the directives, run a dry-check/simulation. Verify that non-global rules will trigger as a "nested traversal" or a "path glob match" rather than firing a "session start" global load reason.

Present a summary of the files you moved, the globs you introduced, and the final line counts of the cleaned-up root files.
```
