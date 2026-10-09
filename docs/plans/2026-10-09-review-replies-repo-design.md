# review-replies as a cross-agent skill repository

Date: 2026-10-09

## Goal

Publish a personal skill for answering review comments so it installs in the widely used coding
agents, the same way [pr-evidence](https://github.com/ArefMozafari/pr-evidence) does.

## Decision

One repository with the pr-evidence layout, so each agent's installer finds the same `SKILL.md`:

```
.claude-plugin/marketplace.json   Claude Code marketplace; Copilot CLI reads it too
.claude-plugin/plugin.json        Plugin manifest; the plugin root is the repository root
.cursor-plugin/marketplace.json   Cursor marketplace
.cursor-plugin/plugin.json        Cursor plugin manifest
gemini-extension.json             Gemini CLI extension manifest
skills/review-replies/SKILL.md    The skill; `skills/*/SKILL.md` is what npx skills and gh skill discover
```

The reasons behind that layout, and the Cursor manifest constraints, are recorded in the
pr-evidence design document and hold here unchanged.

### Name

The personal skill was called `answering-review-rounds`. The public name is `review-replies`:
shorter, and "round" is less common outside GitLab-heavy teams.

### Generalized from the personal skill

- The `user-invocable` frontmatter key is gone; the Agent Skills reference validator rejects it.
- A note pointing at one private project's memory file is gone. The reply language is still the
  team's own, read from its threads.
- "One line saying what `a` applies" named a personal shorthand for approval. It now reads "one
  line naming exactly what the user's go-ahead applies: which edit, in which file".
- The GitHub fetch only read line comments. GitHub keeps line comments, review summaries, and
  conversation comments in three endpoints, so the skill now fetches all three, paginated. The
  GitLab fetch is paginated as newline-delimited JSON.
- Recording the round in memory is now conditional on the agent keeping memory or notes for the
  project.

The one-thread-at-a-time rule, the four-part item message, and the red flags are unchanged.

## Alternatives rejected

- **One collection repository for several skills.** Each skill should install on its own, and the
  pr-evidence layout is already proven in each agent; repositories link to each other instead.
- **Keeping the name `answering-review-rounds`.** See above.

## Verification

- `agentskills validate`, `claude plugin validate --strict` on both Claude Code manifests, the
  Gemini manifest field check, and the Cursor schema check at `cursor/plugins` commit `ecc249f`
  all pass locally.
- The three GitHub fetch commands were run against a public pull request; each returns one JSON
  array of pages.
- Installs checked against the local checkout: Claude Code (`claude plugin marketplace add` and
  `install` in a throwaway configuration folder; the skill lands in the plugin cache),
  `npx skills add` for the Codex and Cursor targets, and `gh skill install --from-local`.
- Gemini CLI, Copilot CLI, and the Cursor CLI install from GitHub and are not installed on the
  machine used, so their README commands are checked after the first push.
