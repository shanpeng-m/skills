# Engineering Commenting Principles

Use this reference for nontrivial comment additions, broad comment sweeps, or review feedback about unclear code.

## Why Comments Matter

In-code documentation records design information that code cannot express directly. This includes contracts, rationale, constraints, side effects, failure behavior, and cross-module dependencies.

Good documentation reduces two major forms of complexity:

- Cognitive load: maintainers can get the relevant facts without reconstructing them from many files.
- Unknown unknowns: maintainers can see which rules, dependencies, and related code paths matter for a change.

Comments are also valuable for the original author. After a few weeks, implementation details and design rationale are easy to forget.

For AI-assisted development, concise comments can reduce token cost and improve correctness by preserving intent near the code instead of forcing agents to load and infer from broad context.

## Principle

Comments should describe information that is not directly visible from the nearby code. "Nearby code" means the declaration or statement next to the comment, not the entire application.

The goal is to make structure and behavior obvious enough that maintainers can understand and modify code without reconstructing hidden rules from scattered implementations.

## Comments And Abstraction

Comments are part of the abstraction boundary. A declaration only exposes names, types, and signatures; it usually cannot express the full contract.

Interface documentation should let a caller use a class or method without reading the implementation. If using an API correctly requires reading its body, the API is not presenting a useful abstraction.

Use comments to hide complexity, not expose it. Put caller-facing contract in interface comments and maintenance-facing mechanics in implementation comments.

## Documentation As A Design Tool

Write important interface comments while designing or changing the API, not only after the implementation is complete.

If a comment is difficult to write, treat that as a design signal:

- A vague comment may indicate an unclear abstraction.
- A long interface comment full of implementation details may indicate a shallow or leaky API.
- Repeated comments across files may indicate a missing central abstraction or design note.
- A comment that mostly compensates for a bad name may indicate the name should change.

Prefer improving the design when that removes confusion. Keep the comment when the information is inherently not expressible in code, such as rationale, side effects, invariants, units, or cross-module rules.

## Common Objections

Do not accept "good code is self-documenting" as a blanket reason to omit documentation. Clear names and structure are important, but they cannot fully express behavior, return semantics, preconditions, design rationale, or visible failure modes.

Do not accept "there is no time" as a reason to skip important interface documentation. Class and method comments are design work; they often pay for themselves immediately by forcing clearer abstractions and reducing future maintenance cost.

Do not accept "comments go stale" as a reason to avoid comments. Reduce staleness by keeping comments close to the code they describe, avoiding duplicated documentation, and updating comments in the same change as the code.

Do not accept "bad comments are common" as a reason to avoid comments. Delete or rewrite bad comments; do not let poor examples define the standard.

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

Avoid replacing interface comments with tiny extracted methods whose names try to carry a whole paragraph of meaning. A precise comment is often clearer than a long method name that repeats documentation at every call site.

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

## Maintenance Discipline

Maintain comments as part of every code change:

- Update nearby comments when changing behavior.
- Delete comments that no longer add information.
- Prefer one authoritative source for each rule.
- Keep comments close to the declarations or blocks they describe when there is a natural location.
- Use code review to catch stale, misleading, or missing comments.

## Review Checklist

Before finishing, verify that each added comment:

- Adds information not obvious from adjacent code.
- Uses different words than the identifier when possible.
- Is placed where a maintainer will naturally look.
- Separates caller-facing contract from implementation detail.
- Is precise about units, ranges, ownership, nulls, and invariants.
- Explains why when code is surprising.
- Does not compensate for a name or structure that should simply be improved.
- Lets callers understand public abstractions without reading private implementation.
- Has been updated together with any changed behavior it describes.
