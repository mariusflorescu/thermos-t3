# thermos-t3 contributor guide

This repo is Cursor's [thermos](https://github.com/cursor/plugins/tree/main/thermos) plugin, adapted to T3 Code for Claude Code and Codex threads. `UPSTREAM` pins the upstream commit it matches.

Before you edit anything under `skills/`, read [T3-NATIVE.md](./T3-NATIVE.md) in full. It is the translation contract and the definition of done.

## Rules

- Keep upstream wording wherever it is harness-neutral. Change only what names a Cursor mechanism.
- Upstream names stay: `thermos`, `thermo-nuclear-review`, `thermo-nuclear-code-quality-review`, and the two `*-subagent.md` briefs.
- `node scripts/check-t3-native.mjs` must exit 0.

## Syncing upstream

Diff `thermos/` in `cursor/plugins` from the `sha` in `UPSTREAM` to the new commit. Apply each hunk through T3-NATIVE.md, run the checker, then bump `sha`.
