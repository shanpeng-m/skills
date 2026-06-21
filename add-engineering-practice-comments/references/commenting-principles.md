# Engineering Commenting Principles

Use this reference for nontrivial comment additions, broad comment sweeps, or review feedback about unclear code.

## Principle

Comments should describe information that is not directly visible from the nearby code. "Nearby code" means the declaration or statement next to the comment, not the entire application.

The goal is to make structure and behavior obvious enough that maintainers can understand and modify code without reconstructing hidden rules from scattered implementations.

## Avoid Repeating Code

Reject comments that only restate names, types, or statements.

Bad patterns:

- `// Add horizontal scrollbar` above `new JScrollBar(HORIZONTAL)`.
- `// Return current object` above `return obj`.
- `// Get normalized resource names` above `getNormalizedResourceNames`.
- Comments that reuse most words from the target identifier without adding meaning.

Self-check: Could a person who has never seen the system write this comment by only reading the adjacent code? If yes, the comment is probably noise.

Better comments add missing details:

- What "normalized" means.
- What array elements represent.
- What unit a padding or timeout uses.
- Whether a range includes its endpoints.
- Whether null means "unknown", "not loaded", "not found", or "not applicable".

## Lower-Level Precision

Use lower-level comments to make declarations precise.

For fields, parameters, and return values, consider:

- Units.
- Inclusive or exclusive boundaries.
- Sentinel values and null meaning.
- Resource ownership and cleanup responsibility.
- Invariants.
- Map key/value roles and absent-entry meaning.
- Whether values are snapshots, live views, cached values, or durable state.

Prefer noun-oriented descriptions for variables: state what the variable represents. Avoid documenting every assignment path unless the mutation protocol itself is the abstraction.

Example shape:

```java
/*
 * true if a heartbeat has been received since the last election timer reset.
 *
 * Used for communication between Receiver and PeriodicTasks.
 */
private boolean receivedValidHeartbeat;
```

## Higher-Level Intuition

Use higher-level comments to help readers see intent without drowning in mechanics.

Good implementation comments summarize a block at a more abstract level than the code:

```cpp
// Try to append the current key hash to an unsent RPC for the target server.
```

This is better than enumerating every condition in the following `if` statement. It gives readers a frame for judging whether the code implements the intended behavior.

Ask:

- What is this block trying to accomplish?
- What is the simplest useful summary?
- What would help a reader validate the code?
- How did execution reach this unusual branch?

## Interface Documentation

Interface comments define the abstraction. They should let a caller use the module without reading the implementation.

For classes and modules, describe:

- The overall capability.
- What each instance represents.
- Important visible limits, such as single-threaded use or ordering guarantees.
- How related public methods are commonly used together, only when that relationship is not obvious.

For functions and methods, describe:

- Caller-visible behavior in one or two high-level sentences.
- Every parameter and return value precisely enough to avoid guessing.
- Side effects.
- Exceptions, errors, blocking, retries, cancellation, and concurrency behavior visible to the caller.
- Preconditions such as "call `open` before `read`" or "input must be sorted".

Do not include private implementation mechanics in interface comments. If an interface comment must explain implementation details for callers to use the API, consider whether the API is too shallow or leaky.

## Implementation Documentation

Implementation comments help maintainers understand internal code. Most short functions do not need them.

Use them for:

- Major phases inside long functions.
- Complex loops, especially when one iteration performs several coordinated updates.
- Non-obvious bug fixes, compatibility workarounds, performance tradeoffs, or ordering constraints.
- State-machine transitions and recovery paths.
- Rare branches where "why are we here?" is more important than "what does the code say?".

When referencing an issue tracker, keep the comment short and cite the issue id if that id is stable and accessible in the project.

## Cross-Module Decisions

Cross-module rules are rare but important. Document them where future maintainers are likely to discover them.

Good authoritative locations include:

- The enum or schema definition developers must edit.
- The protocol, event, or message definition that creates the coupling.
- A central design note when no code location naturally owns the decision.

At secondary sites, use a short pointer:

```cpp
// See designNotes: "Zombie servers".
```

Avoid copying the same long explanation across files; duplicated cross-module documentation goes stale.

## Review Checklist

Before finishing, verify that each added comment:

- Adds information not obvious from adjacent code.
- Uses different words than the identifier when possible.
- Is placed where a maintainer will naturally look.
- Separates caller-facing contract from implementation detail.
- Is precise about units, ranges, ownership, nulls, and invariants.
- Explains why when code is surprising.
- Does not compensate for a name or structure that should simply be improved.
