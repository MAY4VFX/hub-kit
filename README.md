# hub-kit

Personal hub framework: run **all** your projects with AI agents through GitHub.

- **Flat polyrepo** — every project is a sibling repo under one root (`~/Github`). No nesting, no submodules.
- **Roles as composable layers** (USD-style) — a "role" (engineering, design, teaching, shop…) is a context layer in your hub, not a folder. Each project points at its active role via a committed symlink; switch the role, and the same repo loads with a different agent identity.
- **GitHub = memory** — one Project board + issues with work-records are the shared memory across sessions and machines. Nothing lives only in an agent's head.
- **Mobile → desktop pipeline** — brainstorm an idea on your phone, save it as an `inbox` issue; on desktop `/kickoff` turns it into a repo, a plan, and board-tracked slices.
- **Agent-agnostic** — the canonical context file is `AGENTS.md` (with a `CLAUDE.md` symlink), so Claude Code, Codex, Cursor and any AGENTS.md-compatible runner see the same system. Skills are plain `SKILL.md` markdown; manifests are provided for Claude Code (`.claude-plugin/`), Codex (`.codex-plugin/`) and generic agent runners (`.agents/`).

## Install

**Claude Code** (2 commands):

```bash
claude plugin marketplace add MAY4VFX/hub-kit
claude plugin install hub-kit@hub-kit
```

**Codex / other runners**: clone the repo and point your runner at it — `.codex-plugin/plugin.json` declares `skills: ./skills/`; each skill is a self-contained `SKILL.md` you can also use as a plain prompt.

Then, in any folder, run Claude Code and say:

```
/hub-init
```

The onboarding wizard interviews you (one question at a time), creates your **private hub repo** with your own roles, sets up the GitHub Project board and labels, and leaves a `START-HERE.md` guide in your hub explaining how to live with the system.

## Daily commands

| Command | What it does |
|---|---|
| `/kickoff` | Turn an idea (from your phone's inbox or typed right now) into a project: repo + plan + board slices |
| `/route` | File a task/idea into the right repo with the right labels — never the wrong backlog |
| `/dept` | Switch the active role of a project (with proper handoff of open issues) |
| `/sync` | End of session: write work-records into issues, update the board, push the hub |
| `/project-register` | Connect an existing repo (or `--scan` them all) into the system |
| `/hq-doctor` | Verify & repair the system's invariants (symlinks, registry, labels) |

## Development cycle skills

| Command | What it does |
|---|---|
| `/to-issues` | Break a plan into tracer-bullet vertical slices as board-tracked issues |
| `/triage` | Move issues through a state machine; write agent-ready briefs for AFK work |
| `/implement` | Take a ready-for-agent issue to a committed, reviewed, work-recorded result |
| `/research` | Background research against primary sources → cited markdown |
| `/prototype` | Throwaway prototype that answers one design question (logic or UI) |
| `/diagnosing-bugs` | Feedback-loop-first discipline for hard bugs |
| `/grill-me` / grilling | Relentless interview to stress-test a plan |
| `/handoff` | Pause work: handoff comment in the issue, survives machine switches |
| `/weekly` | `--plan` / `--retro` weekly rituals (W-labels, board sweep) |
| `/ship` | Release: notes from commits, tag, push, watch the deploy to "alive" |
| `/learn` | Turn a session lesson into a rule in the RIGHT context layer |
| `/writing-great-skills` | Meta: author new skills well |

Skills marked in their footer are vendored or adapted from [mattpocock/skills](https://github.com/mattpocock/skills) and [serejaris/personal-corp-skills](https://github.com/serejaris/personal-corp-skills) (both MIT).

## How it works

Your hub is a small private repo (created by `/hub-init`):

```
<you>-hub/
├── CLAUDE.md          # agent identity + rituals + ## Hub Config (machine-readable)
├── HQ.md              # registry: | role | repo | local path | domain |
├── START-HERE.md      # your personalized guide
└── departments/       # one .md per role — the "hats" agents wear
```

Each registered project carries two tiny things: an identity block in its `CLAUDE.md` and a committed relative symlink `.dept.md → ../<you>-hub/departments/<role>.md`. When an agent starts in the project, it composes: project context + active role rules + hub rituals. `git log .dept.md` is literally the project's migration history between roles.

## License

MIT
