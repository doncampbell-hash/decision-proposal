# [Feature]

Status: approved [with changes] [YYYY-MM-DD] (`[path/to/proposal].html`, decisions in its notes panel). Extends `[other-spec].md`. [N] PRs, below.

## The rule

[The invariants in prose. What is per-[thing] and what is shared. Limits as named constants (`MAX_X = 200`, decision 1). Uniqueness and why it's needed. What is never shown to whom (decision 6). What happens to old rows.]

## 1 · [Slice name] (one PR[, security review])

- [Migration: what it adds; what it checks first; what it refuses to do — it never deletes data to make itself pass.]
- [`functionName(tx, principal, { … })`: what it takes, the order it does things in, what it returns, what it audits.]
- [API: request shape, status codes, error codes (`too_many_x`), idempotency.]
- [Tests (real dependencies, not mocks): each rule above, each refusal, each edge case from the proposal's frames.]

## 2 · [Slice name] (frames [1a, 1b])

- [Components, routes, the copy from the states table.]
- [The end-to-end test that proves the golden path.]

<!-- As each slice lands, append to its section:
- **As built**: [what differs from the plan above and why; anything a security review added.] -->

## Not in this work

[Everything deferred, by name, so nobody assumes it's covered.]

## Docs

[Which user-facing pages each PR updates.]
