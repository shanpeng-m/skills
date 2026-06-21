---
name: add-engineering-practice-comments
description: Add, improve, or review source-code comments according to engineering-practice standards. Use when the user asks to append/add comments, improve code comments, document interfaces, explain non-obvious implementation choices, clarify data structure fields, record cross-module rules, or address review feedback that code behavior is not obvious.
---

# Add Engineering Practice Comments

Use this skill to add comments that explain information a future maintainer cannot reliably infer from the nearby code declaration or statement.

## Core Rule

Write comments only when they add information beyond a direct reading of adjacent code.

Prefer comments that explain:

- What abstraction the code exposes to callers.
- Why a non-obvious path, workaround, ordering rule, or constraint exists.
- What a value represents, including units, ownership, null meaning, invariants, ranges, and boundary semantics.
- How multiple modules must stay coordinated.

Avoid comments that merely translate names or statements into prose.

## Workflow

1. Inspect existing code and local comment conventions before editing.
2. Classify each candidate comment as interface, data member, implementation, or cross-module.
3. Add the smallest useful comment at the natural discovery point.
4. Prefer renaming unclear code before adding a comment when a better name can remove the need.
5. Keep implementation details out of interface comments.
6. Re-read each added comment against the nearby code and delete it if a first-time reader could write the same comment without understanding the system.
7. Run the relevant formatter or test command when comments affect generated docs, lint rules, or public declarations.

For larger comment passes, ambiguous cases, or review-quality sweeps, read `references/commenting-principles.md` before editing.

## Comment Types

### Interface Comments

Place interface comments before public classes, structs, modules, functions, methods, commands, exported constants, and deep private APIs.

Document the caller-facing contract:

- Overall behavior and abstraction.
- Parameter meanings, constraints, relationships, units, ranges, and ownership.
- Return value semantics, including partial results and sentinel values.
- Side effects that affect later system behavior.
- Exceptions, errors, cancellation, blocking, retries, or concurrency behavior visible to callers.
- Preconditions that callers must satisfy, especially required call ordering.

Do not describe internal algorithms, private data structures, RPC layouts, caches, or scheduling mechanics unless the caller must know them to use the API correctly.

### Data Member Comments

Place data member comments beside fields, instance variables, static variables, and important long-lived local state.

Describe what the value represents, not the code paths that mutate it:

- Units: bytes, pixels, milliseconds, rows, characters, requests per second.
- Boundary semantics: inclusive, exclusive, first unprocessed item, one-past-end.
- Null or empty meaning.
- Ownership and lifecycle responsibilities.
- Invariants such as sortedness, non-emptiness, uniqueness, or monotonicity.
- Map or tuple structure, including key and value roles and what absence means.
- Threading or synchronization role when relevant.

### Implementation Comments

Use implementation comments inside functions only for code blocks whose intent or reason is not obvious.

Good implementation comments usually sit above a block or loop and describe:

- The high-level phase or goal of the block.
- Why the code must exist.
- How the block fits into a larger state machine, protocol, retry policy, or recovery path.
- The trigger that brought execution to this unusual branch.
- A bug, compatibility issue, or external rule that explains non-obvious code.

Do not comment every statement. For complex loops, describe what each iteration accomplishes at a higher level than the loop body.

### Cross-Module Comments

Use cross-module comments for design decisions that several files must honor.

Prefer one authoritative location that future maintainers will naturally visit, such as:

- An enum or schema definition that requires synchronized updates.
- A protocol or event definition.
- A central design note when no single code location owns the rule.

At dependent call sites, add short pointer comments to the authoritative note instead of duplicating the full explanation.

## Quality Bar

A useful comment answers at least one maintainer question that the nearby code does not answer:

- What does this value mean exactly?
- What can the caller rely on?
- What must the caller do first?
- What side effect should I expect?
- Why is this branch necessary?
- Why must operations happen in this order?
- What other files must change with this one?

Treat reviewer confusion as evidence that something is not obvious. Fix it with clearer code, a better name, a focused comment, or some combination of those.
