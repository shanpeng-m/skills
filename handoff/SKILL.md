---
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up. Use when the user asks for a handoff, next-session summary, fresh-agent continuation note, context transfer document, or explicitly invokes $handoff, especially with an argument describing what the next session will be used for.
---

# Handoff

Create a concise handoff document that lets a fresh agent continue the work without rereading the full conversation.

## Invocation Arguments

Treat any user-provided arguments as the next session focus. Use them to decide what details to emphasize, what risks to call out, and which suggested skills to list first.

Argument hint:

```text
What will the next session be used for?
```

## Output Location

Save the handoff document to the user's OS temporary directory, not the current workspace.

Use the platform temp directory from the environment or runtime:

- Prefer `$TMPDIR` when set.
- Otherwise use `/tmp` on Unix-like systems.
- Use a descriptive filename such as `codex-handoff-YYYYMMDD-HHMMSS.md`.

After writing, tell the user the full path.

## Content Rules

- Summarize only what a fresh agent needs to continue.
- Do not duplicate content already captured in artifacts such as PRDs, plans, ADRs, issues, commits, diffs, generated docs, or code changes. Reference those artifacts by path, URL, branch, commit, or issue/PR number instead.
- Include concrete file paths and commands when they are needed to resume safely.
- Preserve unresolved decisions, blockers, assumptions, and verification state.
- Include enough repo/workspace state to avoid accidental overwrite or duplicated work.
- Redact sensitive information before writing the file.

## Redaction

Redact secrets and personal data. Replace values with clear placeholders such as `[REDACTED_API_KEY]` or `[REDACTED_PERSONAL_EMAIL]`.

Redact at least:

- API keys, access tokens, refresh tokens, session cookies, SSH keys, private keys, certificates, and passwords
- Database URLs or connection strings containing credentials
- Personal email addresses, phone numbers, physical addresses, government IDs, and other personally identifiable information
- Internal credentials copied from env files, logs, CI output, browser sessions, or chat messages

Keep non-sensitive paths, branch names, filenames, command names, and public URLs when useful.

## Suggested Structure

Use this structure unless the next-session focus clearly calls for a tighter variant:

```md
# Handoff

## Next Session Focus
- ...

## Current State
- ...

## Key Artifacts
- ...

## Decisions And Assumptions
- ...

## Open Questions / Blockers
- ...

## Suggested Skills
- `$skill-name`: why the next agent should invoke it.

## Relevant Commands
- ...

## Verification
- Completed: ...
- Not run: ...

## Cautions
- ...
```

## Suggested Skills Section

Always include `## Suggested Skills`.

Recommend skills based on the next-session focus and current context. Include only skills that are likely to help the next agent.

Examples:

- `$local-bug-fix`: for locally reproducible failures or regressions.
- `$interview-plan`: for continuing plan/design decision interviews.
- `$skill-creator`: for continuing skill creation or refinement.
- `$github:gh-fix-ci`: for GitHub Actions failures.
- `$github:gh-address-comments`: for PR review feedback.
- `$vercel:nextjs`: for Next.js App Router work.
- `$browser:browser`: for local browser verification.

If no specialized skill is appropriate, say:

```md
- None required beyond normal Codex coding workflow.
```

## Workflow

1. Identify the next-session focus from invocation arguments, if present.
2. Inspect current conversation context and any directly relevant local artifacts.
3. Check current workspace state when relevant, for example `git status --short` and paths mentioned in the conversation.
4. Build the handoff around references to existing artifacts, not copied content.
5. Redact sensitive data.
6. Write the Markdown file to the OS temporary directory.
7. Reply with the file path and a short summary of what it covers.

## Quality Bar

The handoff is complete when a fresh agent can answer:

- What is the next objective?
- What has already been decided or changed?
- Where are the source artifacts?
- What remains unresolved?
- Which skills should be invoked first?
- What verification has or has not been done?
