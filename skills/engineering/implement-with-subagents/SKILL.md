---
name: implement-with-subagents
description: "Summon subagents to implement a piece of work based on a PRD or set of issues."
disable-model-invocation: true
---

# Implement with Subagents

Work through a set of issues one at a time, **delegating** each to a fresh subagent so the parent's context is spent on coordination, not implementation. For each issue the parent picks the next **frontier** ticket, cuts a reviewable branch off the last issue's work, and **dispatches** a subagent that implements, commits, and writes the issue back.

## Process

### 1. Pick the next frontier issue

Take the next unblocked issue in the set. Read its full body — you will hand it to the subagent, so carry everything: title, acceptance criteria, blocking context, any linked spec.

**Done when** the issue's identifier, title, and full body are in hand.

### 2. Cut a reviewable branch

Branch off the prior issue's work so each issue is reviewable in isolation. From the **last issue's branch** if one exists; otherwise from the current branch. Name the branch after the issue.

```sh
git checkout -b <issue-name> <last-issue-branch|HEAD>
```

**Done when** you are on the new branch, carrying the prior issue's work (if any) as its starting point.

### 3. Dispatch the subagent

Summon a `general` subagent with a single `Agent` tool call. Pass it everything it needs in the prompt — the issue body, the branch name, and any spec the set is working from — so it never returns for more. Instruct it to:

- Follow `/implement` to implement the issue.
- Tick every acceptance-criterion checkbox in the issue it has satisfied.
- Post a **resolution summary** on the issue, written for an agent or human to review next.
- Add and commit all changes to this branch (per `/implement`).

**Done when** the subagent has returned, reported its summary, and `git status` is clean on the branch.

### 4. Report to the user

Return the subagent's summary. The issue is already updated.

**Done when** the user has the summary in front of them.

Repeat from step 1 for the next issue in the set, branching off this issue's branch.

