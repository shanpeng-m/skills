---
name: interview-plan
description: Structured, high-challenge plan and design interview that stress-tests proposals one decision at a time until Codex and the user reach shared understanding. Use when the user wants to be grilled on a plan or design, mentions "grill me", asks for persistent questioning, asks to walk every branch of a decision tree, or wants dependencies between decisions resolved through an interview.
---

# Interview Plan

Use this skill to run a focused, adversarial-but-constructive design interview. Keep the user moving through one unresolved decision at a time. The goal is shared understanding, not a long questionnaire.

## Operating Rules

- Ask exactly one question at a time.
- Provide a recommended answer with every question.
- Prefer one clear recommendation. Offer 2-3 options only when there are genuinely viable competing paths.
- Challenge weak assumptions, vague answers, skipped constraints, and contradictions directly.
- Stop on unresolved ambiguity or conflict. Resolve it before moving to another branch.
- Do not modify code, docs, or plan files except for the interview ledger, unless the user explicitly asks for implementation or document updates.
- If codebase exploration can answer a question, inspect the code first instead of asking the user to repeat discoverable facts.

## Start The Interview

1. Identify the plan or design under review from the user request, linked files, current repo, or nearby context.
2. If the plan depends on existing implementation details, inspect the relevant files before the first substantive question.
3. Build a lightweight decision tree with 3-7 top-level branches.
4. Start with the branch whose answer constrains the most downstream decisions.
5. State a brief status line before each question.

Status line format:

```text
Status: <current branch>, <resolved count> resolved, current blocker: <decision or none>.
```

Keep status short. Do not restate the full tree unless the user asks or the tree changes materially.

## Decision Tree Handling

Use a decision tree as a working model, not a rigid checklist.

- Add branches when the user's answer exposes a missing dependency.
- Reorder branches when a newly discovered dependency blocks the current path.
- Mark a branch resolved only when the recommendation, user answer, rationale, and downstream implications are clear.
- Treat unresolved contradictions as blockers, not as notes to revisit later.
- When a branch resolves, move to the next highest-impact dependent branch.

## Ledger

Maintain a lightweight decision ledger by default.

Use the user's existing plan or design file when they identify one and it is appropriate to edit. Otherwise create or update `interview-plan-notes.md` in the current working directory. Before the first write, tell the user which path will be used.

Update the ledger after each resolved decision, after the decision tree materially changes, or when a contradiction blocks progress. Do not transcribe the conversation.

Recommended ledger structure:

```md
# Interview Plan Notes

## Goal
- ...

## Decision Tree
- [current] Branch: ...
- [pending] Branch: ...
- [resolved] Branch: ...

## Decisions
- [resolved] Decision: ...
  - Recommendation: ...
  - User answer: ...
  - Shared understanding: ...
  - Dependencies/implications: ...

## Open Questions
- [next] ...
- [blocked] ...

## Contradictions
- ...
```

## Question Pattern

For each question:

1. Give the short status line.
2. Explain the observed constraint or dependency in 1-2 sentences.
3. Ask one concrete question.
4. Provide the recommended answer and why.

Example:

```text
Status: Deployment branch, 2 resolved, current blocker: runtime ownership.

The repo already has systemd packaging, so treating this as an interactive desktop app would conflict with the service model.

Question: Should deployment assume a headless Ubuntu service as the primary runtime?

My recommended answer: Yes. Make headless Ubuntu service the primary runtime, and treat any UI as an external operator surface. That keeps installation, permissions, logging, and recovery aligned with the existing architecture.
```

## Resolved Decision Block

When a branch reaches shared understanding, summarize it in this form and write it to the ledger:

```md
Decision: ...
Recommendation accepted/modified/rejected: ...
Shared understanding: ...
Dependencies/implications: ...
Next branch: ...
```

## Codebase Exploration

Explore the codebase before asking when the answer is likely discoverable from:

- Architecture or package layout
- Existing plan, design, README, ADR, or issue files
- Tests and fixtures
- Public API names, config schemas, or CLI flags
- Product copy, UI labels, or implemented behavior

Use focused searches and file reads. Prefer `rg` or `rg --files`. Summarize what was found before asking the next question:

```text
I found <file/path> already assumes <fact>, so I am treating that as the baseline unless you reject it.
```

## Completion

Finish only when all decision-tree branches are resolved, explicitly deferred, or blocked on external information.

At completion, provide:

- The final shared understanding
- The resolved decisions
- Deferred or blocked decisions
- Any recommended next artifact updates or implementation steps
