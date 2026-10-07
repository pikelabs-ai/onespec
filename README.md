# OneSpec

**One shared spec for your team and every AI it works with.**

OneSpec is a small practice, plus the tools that keep it honest, for writing down what a system
is and why it is that way. It lives in a `doc/spec/` folder next to the code, in plain Markdown.
Any engineer and any coding agent, whatever the model, reads it first and works from it.

---

## Why

Code tells you *how*. It doesn't tell you what the system is for, why one design beat another, or
what the words in the domain mean. That knowledge lives in people's heads and in old chat sessions,
and every new session, human or AI, starts without it.

A spec fixes that, if it stays current and stays small. OneSpec gives you four things:

1. **One mental model for people and agents alike.** A new engineer or a fresh AI session gets
   oriented from a handful of documents in minutes, instead of reverse-engineering the code.
2. **Living, not bloated.** One rule decides when to write (below), and the default answer is
   no. Most changes don't move the system's shape, so most changes don't touch the spec.
3. **Decisions that survive.** Load-bearing choices become short decision records that are never
   edited, only replaced. The reasons outlive turnover, reorganizations and context windows.
4. **No allegiance to any tool or model.** It is Markdown and a few conventions. Your team can use
   Claude, GPT, Gemini or whatever comes next, side by side, and the spec carries forward intact.

## What it is not

OneSpec does not tell an AI how to plan, think or break down work. Some spec tools put the model
through a fixed sequence (specify, plan, task, implement). That kind of scaffolding gets in the
way as models improve.

OneSpec manages **what the team knows**, not **how the work gets done**. Better models make
that more valuable, not less: no model, however capable, can recover a decision that was never
written down. And the better models get at writing, the more a project needs a rule about when
*not* to write.

The test for every feature in this project: *would it still be useful if models were ten times
smarter?*

## The shape

```
doc/spec/
  README.md          the rules for this repo (the threshold rule, what goes where)
  INDEX.md           generated routing map: one line per doc, read this first
  RELATED.md         optional: specs in other repos this one depends on
  backlog.md         known work you owe but haven't scheduled (mutable)
  purpose/           why the system exists: overview, glossary
  constitution/      how we build here: conventions, tech stack, standing rules
  design/            what we're building: architecture, one doc per area, status
  adr/               why this over that: numbered decision records, never edited
```

Everything else in `doc/` stays free-form. The rules apply only inside `doc/spec/`.

A few conventions carry most of the weight:

- **Each design doc names the code it governs** in its frontmatter (`owns:`), so a change to a file
  can be traced to the doc that describes it, and a doc pointing at deleted code gets caught.
- **Build status lives in one place**, `design/status.md`. Status notes scattered through design
  docs are the first thing to go stale.
- **Decision records are immutable.** To change a decision, write a new record that replaces the
  old one. When you cite a record from another repo, name the repo: `billing ADR-0004`, never a
  bare number.

## The one rule

> Touch the spec only if a reader tomorrow would form a materially different picture of the
> system without the change.

The default is no. Bug fixes, refactors, renames, new tests and dependency bumps almost never
qualify. New components, new seams between parts, reversed decisions and new conventions usually
do. The rule is as much about when not to write as when to.

## Who writes it

People, agents, or both together. The best spec content usually comes out of a conversation: a
developer and a coding agent working through a design question, with the agent writing down what
was decided and why while the reasons are still fresh. The diff shows what changed. Only the
conversation knows why.

The agent can draft and a person corrects, or a person writes and the agent checks it against
the code. Either way it is one document that both read.

## Getting started

Install the command-line tool (Python 3.9+, no dependencies):

```
pipx install onespec
```

Then, from the root of your repo, start a spec with your coding agent:

```
onespec init
```

`init` sets up the empty structure and installs the instructions your agents follow. Then ask your
agent to **create the spec** (`/create-spec` in tools that support commands; in others, just ask
it to "create the spec following doc/spec/README.md"). What happens next depends on where you are:

- **Existing code, no spec.** The agent reads the code and drafts the overview, the architecture
  and the decisions it can see evidence for. Then it asks you what the code can't tell it: what
  the system is for, why the odd parts are odd, which decisions were deliberate. You correct the
  draft together. Expect an hour, not a day.
- **New project.** Brainstorm with the agent as you normally would. As the design firms up, the
  agent records the purpose, the architecture and each decision as it is made. The spec ends up
  as the part of the brainstorm that is worth keeping.
- **A design already written elsewhere.** Point the agent at it. The agent turns it into the
  overview, architecture and decision records, so the agreed design stays current once code lands
  instead of becoming a document nobody reopens.

## Day to day

**Before a commit, ask your agent to update the spec** (`/update-spec`). It reads the change and
the session behind it, applies the threshold rule, and either edits the one or two files that
should move or tells you no update is needed. "No update needed" is the common, correct answer.

**The mechanical checks need no model.** `onespec check` verifies that frontmatter is valid,
`INDEX.md` is current, every `owns:` path still exists, no two decision records share a number,
and status lives only in `status.md`. Run it locally, in a git hook, or in CI.

```
onespec index     # rebuild INDEX.md
onespec check     # run the mechanical checks
```

A commit hook is available but only advises by default. Put `[skip-spec]` in a commit message to
record that you considered the spec and nothing needed to change.

## Teams and branches

When two branches both change the spec, ask your agent to **reconcile the spec**
(`/reconcile-spec`). It renumbers clashing decision records and regenerates the index. Where both
branches edited the same design doc, it merges the meaning rather than the lines. Where the two
branches actually made *opposing* decisions, it does not pick a winner: it stops and puts the
conflict in front of the people who made them.

## Large repos

In a monorepo, give each major part its own `doc/spec/`, and keep one at the root that describes
how the parts fit together and points to each part's spec. Decision records are numbered per
tree, and cited with the tree's name when referenced from elsewhere, the same as across repos.
`onespec index` at the root finds the nested specs and lists them.

## Works with any agent

The rules are written for any reader, not for one product. `onespec init` writes a short
section into `AGENTS.md`, which most coding agents read. It can also add small adapters for tools
that need their own files: Claude Code (`CLAUDE.md` and commands), Cursor rules, GitHub Copilot
instructions. The adapters only point at `doc/spec/`; the substance is never duplicated.

## Status

Beta. The practice has run since spring 2026 across nine repositories, from a coding-agent
harness to a production data service, including specs written before the code, alongside it and
after it. The tooling around it is new.

More at [pikelabs.ai/onespec](https://pikelabs.ai/onespec).

## License

Apache License 2.0. Copyright 2026 Pike Labs, Inc.
