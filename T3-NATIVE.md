# T3-native contract

Thermos upstream (`cursor/plugins`, path `thermos/`) is written for Cursor. This repo ships the same skills adapted to T3 Code, for Claude Code and Codex threads alike. This file is the translation contract. Every skill follows it, and every upstream sync applies it to the incoming change. When upstream text and this contract disagree, this contract wins. It follows the same rules as [pstack-t3's contract](https://github.com/mariusflorescu/pstack-t3/blob/main/T3-NATIVE.md), narrowed to what thermos uses.

Keep upstream wording wherever it is harness-neutral. Change only what names a Cursor mechanism. `node scripts/check-t3-native.mjs` enforces the mechanical part.

## What reaches an agent

`npx skills add mariusflorescu/thermos-t3 -g -a claude-code -a codex` copies each `skills/<name>/` directory into `~/.agents/skills/<name>/` (Codex reads it there) and symlinks `~/.claude/skills/<name>` to it (Claude Code reads it there). Nothing else in this repo reaches an agent. A skill links only inside its own directory or to a sibling skill (`../<other-skill>/...`). Install globally or per project, never both: Codex resolves neither copy on `$<name>` when a name exists in both places.

## Harness facts

Probed in T3 Code on 2026-10-10 (details in pstack-t3's contract).

| Fact | Claude Code | Codex |
|---|---|---|
| Skill folders | `~/.claude/skills/`, `<cwd>/.claude/skills/` | `~/.agents/skills/`, `.agents/skills/` from the project root to the cwd |
| Where a loaded skill's location shows | `Base directory for this skill: <dir>` | `<path><dir>/SKILL.md</path>` |
| `disable-model-invocation: true` | Hidden from the model; its skill tool cannot load it | Ignored; `agents/openai.yaml` with `policy.allow_implicit_invocation: false` hides it, and `$<name>` still loads it |
| How the user runs a skill | `/<name>` | `$<name>` |

A `delegate_task` child runs in the parent's workspace and gets only its brief. `task_status` reaches only tasks the calling thread created.

## Skills, paths and briefs

- A skill named in bold or in backticks, or run as `/<name>`, in agent-facing text is the file `../<name>/SKILL.md`, resolved from the naming skill's base directory. A skill that names another one carries the standard sentence saying so (the checker enforces it).
- Every user-only skill keeps `disable-model-invocation: true` and ships `agents/openai.yaml` with `policy.allow_implicit_invocation: false`.
- Frontmatter `name` equals the directory name. Cursor UI fields go.
- A brief that needs a child to follow a skill gives the absolute path of that `SKILL.md`. Never start a brief with `/<name>` or `$<name>`.

## Delegation

A subagent is a `delegate_task` child: `mode: "async"`, a `title` naming its pass, a `clientRequestId` stable across retries of one round and new per round, `runtimeMode` inherited, no `target` unless the user names a model (upstream sets none). The parent ends its turn after spawning and the completion notifications wake it.

## Translation table

| Cursor / upstream | T3 |
|---|---|
| `Task` tool, `subagent_type: "<agent>"`, `run_in_background: true` | `delegate_task` per Delegation. |
| `agents/thermo-nuclear-review-subagent.md` | `skills/thermo-nuclear-review/references/thermo-nuclear-review-subagent.md`, pasted at the top of the child's brief with its `SKILL.md` placeholder filled. |
| `agents/thermo-nuclear-code-quality-review-subagent.md` | `skills/thermo-nuclear-code-quality-review/references/thermo-nuclear-code-quality-review-subagent.md`, same use. |
| "You are a Task subagent", agent frontmatter | "You are a delegated reviewer (a `delegate_task` child)". No frontmatter in a brief. |
| "Load the `<skill>` skill (shipped in the Thermos plugin)" | Read the `SKILL.md` path the parent filled in. |
| Parent's parallel `Task` calls with `subagent_type: "shell"` / `"explore"` to gather the diff | The parent gathers `git diff <base>...HEAD` and the changed files itself, or with a read-only `delegate_task` child when they are large. |
| "Do not spawn nested subagents" | "Do not delegate further". |
| `/add-plugin thermos`, `.cursor-plugin/`, `assets/` | Dropped. Install with `npx skills add`. |
| BugBot, `gh` / `glab` | Unchanged: GitHub and GitLab review bots and CLIs. |

## Definition of done

`node scripts/check-t3-native.mjs` exits 0. It fails on Cursor mechanisms in skill text, a broken or escaping relative link, a frontmatter `name` that differs from its directory, a user-only skill without the matching `agents/openai.yaml` (or the reverse), and a skill that names another skill without the standard sentence.
