# Coding Discipline

Three rules that keep AI coding agents honest. Nothing else.

An agent-discipline prompt/skill for Claude Code, ChatGPT, Codex, OpenClaw, Hermes, or
any LLM coding workflow. You paste 17 lines into your agent's system prompt or
rules file. It stops the two failures that eat AI-assisted projects: editing code
without understanding the running system, and declaring "done" on a plausible
summary instead of verified output.

Distilled from ~2 months and several thousand agent runs in production on a
multi-project server (Docker apps, systemd services, live websites, autonomous
agent pipelines). The long version of every failure mode is boring. The fix fits
on an index card.

## The three rules

1. **Study the technology before touching the code.**
   Read how the live system actually runs. If you do not understand it, you do not edit it.

2. **A job can take more than one run. A todo list is a must.**
   Track the real steps. Do not fake "done" to close the turn.

3. **Never trust a subagent. Always give him the context.**
   A child summary is a self-report. Verify side effects yourself. Fat brief, explicit bar.

And the closing law:

> HTTP 200 is not a person finishing the job. Do not delete a live app to make a session smaller.

## Why it works

Every AI coding failure the author has watched reduce to one of three holes:

- The agent edits code it never ran and never understood, because the code "looked standard".
- The agent runs out of turn budget, deletes its todo list mentally, and reports a
  subset as the whole job.
- The agent delegates to a subagent, the subagent says "done", and nobody checked.

The rules map one to one. No frameworks, no checklists, no maturity models. A rule
list that fits in a tweet survives compression; a 40-section methodology does not.

## When to use it

Use it when an LLM writes, edits, deploys, or debugs code at all:

- Claude Code, Codex CLI, OpenClaw, Hermes, aider, OpenHands, any agentic coding CLI
- ChatGPT / Gemini / Grok chat sessions where the model edits files or runs commands
- Multi-agent setups where a parent agent spawns children ("subagents")
- CI pipelines where an LLM agent opens PRs or fixes failing tests
- Long projects spanning multiple sessions, where context gets lost between runs

Skip it when: one-shot throwaway scripts, or a fully gated human-reviewed pipeline
where nothing ships without eyes on the diff. Even then, rule 1 costs nothing.

## How to use

### Option A: paste into an instructions file (recommended)

Drop SKILL.md content into whatever instructions file your tool already reads:

| Tool | File |
|------|------|
| Claude Code | `CLAUDE.md` (repo root) |
| Codex CLI | `AGENTS.md` (repo root) |
| OpenClaw | `AGENTS.md` |
| Hermes | a skill under your `skills/` dir |
| ChatGPT / Gemini / Grok chat | paste as first message or custom instructions |
| Cursor / Windsurf | `.cursor/rules` or equivalent rules file |

### Option B: minimal paste

Paste this into your agent's instructions:

```
CODING DISCIPLINE, three rules:
1. Study the technology before touching the code. If you don't understand how the
   live system runs, you don't edit it.
2. A job can take more than one run. Keep a todo list of the real steps. Never fake
   "done" to close the turn.
3. Never trust a subagent. A child summary is a self-report. Give fat context, an
   explicit quality bar, and verify side effects yourself.
Law: HTTP 200 is not a person finishing the job. Don't delete a live app to make a
session smaller.
```

### Option C: as a Hermes skill

Copy `SKILL.md` into your Hermes skills directory. It triggers on every coding task
by frontmatter description: `Use for every coding task. Three rules only.`

## What changes when you run it

- The agent reads the live system before writing code (probes, status endpoints,
  running processes), not just the repo files.
- The agent keeps a real todo list across runs instead of resetting every turn.
- Subagent "done" reports get verified against actual side effects before anyone
  calls the job finished.

## Results

Ran as the house discipline on the author's production agent host since 2026-08-26:

- Thousands of agent runs across Docker apps, systemd services, live websites, and
  multi-agent pipelines; three rules carried all of it.
- Fake-done claims (a summary standing in for a job) dropped to near zero once rule 2
  plus the HTTP-200 law were enforced on every session.
- Subagent trust failures dropped: every child report is now verified against real
  side effects before "done" is claimed.

The rules are deliberately small so you can enforce them; a discipline you cannot
recite is a discipline you will not apply.

## Compatibility

Works with anything that reads instructions: agent CLIs, chat models, CI agents,
multi-agent frameworks. Nothing is runtime-specific:

- **Claude Code**: drop into `CLAUDE.md`, works with subagents and hooks. Tested as
  the live discipline on Claude Code sessions.
- **ChatGPT**: paste into Custom Instructions or the first message of a chat; the
  three rules constrain code edits and "done" claims in both.
- **Codex CLI**: `AGENTS.md`, and the subagent rule covers delegated tasks.
- **Hermes Agent**: load as a skill; frontmatter triggers on any coding task.
- **OpenClaw / aider / OpenHands / Cline / Cursor / Windsurf**: instructions file or
  rules file, same three rules.

## FAQ

**Why only three rules?**
Because a discipline you can recite gets applied. Methodology documents grow until
nobody reads them. Compression is the feature.

**Rule 3 says "give him the context", why "him"?**
It reads as deliberate; the rule is about the subagent as a worker you owe a fat
brief and an explicit bar. Keep the wording or change it, the mechanics do not care.

**Does this replace testing?**
No. It makes testing happen. Rule 1 forces understanding before edits, which is
where the test plan comes from. Rule 2 forces the verify gate to actually run
before "done" is claimed.

## License

MIT.
