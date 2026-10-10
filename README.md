# Thermos for T3 Code

Thermo-nuclear branch review for coding agents: a deep correctness and security audit, a harsh maintainability rubric, and `thermos`, which runs both as parallel reviewers and merges their findings. This is Cursor's [thermos](https://github.com/cursor/plugins/tree/main/thermos) plugin adapted to [T3 Code](https://github.com/pingdotgg/t3code), for Claude Code and Codex threads alike.

## Install

```bash
npx skills add mariusflorescu/thermos-t3 -g -a claude-code -a codex
```

This puts one copy of each skill in `~/.agents/skills/` (Codex reads it there) and a symlink in `~/.claude/skills/` (Claude Code reads it there). Update with `npx skills update -g`. Install globally or per project, not both: when a skill name exists in both places, Codex resolves neither on `$<name>`.

## Use

| Skill | Use it when |
|---|---|
| `/thermos` | You want both reviews of a branch in parallel and one deduplicated verdict. |
| `/thermo-nuclear-review` | You want a deep audit of a branch's changes for bugs, breakages, security, devex regressions and feature-gate leaks. |
| `/thermo-nuclear-code-quality-review` | You want a harsh maintainability review: code-judo restructurings, the 1k-line rule, spaghetti growth, boundaries. |

Codex threads type `$<name>` where this README writes `/<name>`. All three are user-only: they run when you type them, never on their own.

## How it works on T3

`/thermos` gathers the diff and the changed files, then spawns two `delegate_task` children in one turn. Each child's brief opens with its reviewer file (`references/*-subagent.md` in the matching rubric skill) and the absolute path of that rubric's `SKILL.md`, followed by the diff and file contents. The children run on your thread's model, and their completions wake the parent, which merges the findings.

[T3-NATIVE.md](./T3-NATIVE.md) maps every Cursor mechanism upstream uses to its T3 equivalent. `node scripts/check-t3-native.mjs` enforces it.

## Differences from upstream

- Cursor's `Task` subagents become `delegate_task` children. The two agent definitions become briefs inside the skills they serve.
- Every skill ships `agents/openai.yaml` so Codex hides it like Claude Code does.
- Dropped: the Cursor plugin manifest and logo.

## Staying current with upstream

`UPSTREAM` pins the upstream commit this port matches. A daily T3 schedule on the maintainer's machine diffs `thermos/` in [cursor/plugins](https://github.com/cursor/plugins) from that commit, ports any change through T3-NATIVE.md, runs the checker, and pushes to `main`. [AGENTS.md](./AGENTS.md) holds the rules.

## Credit

Adapted from [cursor/plugins thermos](https://github.com/cursor/plugins/tree/main/thermos) by Cursor. This port is not affiliated with Cursor.

## License

MIT. [LICENSE](./LICENSE) keeps the upstream copyright notice.
