# Code Review Standards

This document describes what we look for before any code contribution is approved and merged into production. It applies to every repository under this organization, regardless of language, stack, or product.

Read this before opening a pull request. It will save you review cycles.

---

## Our Philosophy

A code review exists to answer one question: **does this change make the codebase healthier, safer, and easier to operate than it was before?**

That means review isn't a formality, and it isn't a personal judgment of the contributor either. It's a shared responsibility. When a reviewer approves a change, they are co-signing it. If it causes a problem in production, that's a shared outcome, not a single person's mistake. We take that seriously, and we expect contributors to as well.

This also means we're not chasing perfection. A pull request that makes things clearly better, even in a small way, is worth merging. A pull request that introduces risk, confusion, or debt is worth pushing back on, however well-intentioned it is.

---

## What We Look For

### 1. Correctness and Intent

Before anything else: does this change solve the actual problem, in a way that fits how the rest of the codebase works?

- The PR description should explain *why* the change is needed, not just *what* changed.
- The approach should fit existing patterns and architecture. If you're introducing a new pattern, say so explicitly and explain why the existing one didn't fit.
- Solving a problem we don't have, or solving it in a way that fights the existing design, will not be approved regardless of how clean the code is.

### 2. Security

This is non-negotiable, and it applies even if your change feels small or unrelated to security.

- Validate and sanitize all external input. Assume nothing arriving from a user, API, or external service is safe by default.
- Never hardcode secrets, API keys, tokens, or credentials. Use environment variables or the project's existing secret-handling pattern.
- Never log sensitive data (tokens, passwords, personal data, full request/response bodies that might contain either).
- Check authentication and authorization on every new endpoint or capability you add. Don't assume an existing check upstream covers your new code path; verify it.
- If your change touches cryptography, access control, or anything where leaking information indirectly (timing, error messages, side channels) could be a problem, flag this explicitly in your PR description. We will look closely.
- Follow standard secure coding practices (OWASP guidelines are a reasonable baseline if you're unsure).

### 3. Data Safety

If your change touches a database schema, storage, or any operation that can destroy or corrupt existing data:

- Never drop a column, table, or field without first confirming it is empty or has been safely backed up/migrated.
- Always use specific, scoped conditions (e.g. precise `WHERE` clauses) on any update or delete. Broad or unscoped destructive operations will be rejected outright.
- If a migration is involved, the PR description must include a rollback plan. "It should be fine" is not a rollback plan.
- When in doubt, write the migration to be additive and reversible, and handle cleanup in a separate, later change.

### 4. Tests

We care about tests that actually exercise risk, not tests that exist to satisfy a checkbox.

- New behavior needs a test that would fail if the behavior were broken.
- Bug fixes need a test that reproduces the original bug and confirms it's fixed.
- Edge cases relevant to the change (empty input, null/undefined, boundary values, concurrent access where relevant) should be covered, not just the happy path.
- A green test suite that doesn't actually cover the risk in your change will not be treated as sufficient on its own.

### 5. Readability and Maintainability

Code is read far more often than it's written. Optimize for the next person, who may not have any context on why you made the choices you made.

- Name things for what they are, not for how they're implemented.
- Keep functions focused on one responsibility. If you're scrolling to understand a single function, it's probably doing too much.
- Don't introduce abstraction or cleverness that isn't earning its complexity. The simplest solution that correctly solves the problem is usually the right one.
- Add comments to explain *why*, not *what*. The code already says what it does.
- Follow the existing conventions of the repository (naming, file structure, formatting) even if you'd personally do it differently elsewhere. Consistency matters more than individual preference.

### 6. Modularity, Reusability, and Scalability

Code should be built as components, not as one-off solutions glued to a single spot in the codebase.

- Favor small, composable units (functions, modules, components) with a clear single responsibility over large monolithic blocks that do many things at once.
- Before writing something from scratch, check whether a similar capability already exists elsewhere in the codebase. Reuse or extend it rather than duplicating logic. Duplicated logic is duplicated bugs.
- If you're writing something that's likely to be needed again (a UI component, a utility function, a validation rule, an API client), design it to be reusable from the start: clear inputs, no hidden dependencies on where it happens to be called from, no assumptions baked in that only hold true for your specific use case.
- Avoid tight coupling between unrelated parts of the system. A change in one module should not require unrelated changes in modules that have nothing to do with it. If it does, that's a sign the boundaries are wrong.
- Design with growth in mind, not just the immediate need. Ask: if this needs to handle 10x the data, users, or requests, does the approach still hold up, or does it fall over? You don't need to over-engineer for scale you don't have, but an approach that's obviously going to need a full rewrite at the next order of magnitude should be flagged and discussed, not silently shipped.
- Configuration, constants, and environment-specific values belong in config, not hardcoded inline where they'll need to be hunted down later.

### 7. Error Handling and Resource Management

- Every failure path should be handled deliberately, not silently swallowed.
- Errors should be logged with enough context to debug the issue without needing to reproduce it locally.
- Resources (connections, file handles, locks, subscriptions) must be properly acquired and released, including on failure paths.
- If your code can run concurrently with itself or other parts of the system, consider and address race conditions explicitly. Don't assume timing will "just work."

### 8. Performance

- Avoid unnecessary work in hot paths: redundant computation, unnecessary network or database calls, large unbounded loops over user-controlled input.
- If you're uncertain whether a change has a meaningful performance impact, say so in the PR description rather than assuming it's fine.
- Don't optimize prematurely either. Clear, correct code is the priority unless there's a demonstrated performance problem.

### 9. Observability

If we can't tell when this breaks in production, it isn't done.

- User-facing or production-critical changes should include enough logging to diagnose a failure without guessing.
- If the project has existing metrics, tracing, or alerting conventions, follow them for new code paths.
- Ask yourself: if this fails at 3am, will whoever is on call be able to figure out why from the logs alone?

### 10. Scope Discipline

- Keep pull requests focused on a single concern. A PR that fixes a bug, refactors unrelated code, and adds a new feature all at once cannot be reviewed properly, no matter how good each individual piece is.
- If you find an unrelated problem while working on something else, note it (an issue, a comment, a follow-up PR) rather than folding it into your current change.
- Large PRs take longer to review and are more likely to be rejected outright. When in doubt, split it.

### 11. AI-Assisted Contributions

We have no issue with AI-assisted code. We do have a standard for it.

- Fluent-looking code is not the same as correct code. If you used an AI tool to help write part of this contribution, you are still fully responsible for verifying every line works as intended and doesn't reference APIs, libraries, or patterns that don't actually exist in this codebase.
- AI-assisted contributions are held to the exact same bar as any other contribution on every point above, with no exceptions.

---

## What Will Get a Pull Request Rejected or Sent Back

- Destructive data operations without a migration or rollback plan
- Hardcoded secrets or credentials, anywhere, even in test code
- Missing or superficial tests on non-trivial logic
- PRs that mix multiple unrelated concerns
- Code that duplicates existing logic instead of reusing it, or that tightly couples unrelated parts of the system for no clear reason
- Code that contradicts existing conventions without explanation
- Security-relevant changes (auth, access control, cryptography, input handling) without an explicit note calling that out in the PR description
- Copy-pasted or AI-generated code that hasn't been verified to actually work against this codebase

## What Will Not Block a Pull Request

- Minor style preferences already enforced by the project's linter or formatter
- Subjective taste where there is no correctness, security, or maintainability difference
- Small inefficiencies in non-critical code paths, if correctness and clarity are otherwise solid

---

## Before You Open a Pull Request

1. Read the diff yourself, as if you were reviewing someone else's work.
2. Make sure the PR description explains the *why*, not just the *what*.
3. Confirm tests cover the actual risk in your change, not just the happy path.
4. Double-check for anything in §2 (Security) and §3 (Data Safety) above. These are the two areas we are least flexible on.
5. Keep it scoped. If you're not sure whether to split it, split it.

A well-prepared pull request gets reviewed faster and merged faster. This isn't bureaucracy for its own sake; it's how we keep the codebase something everyone can trust and build on.
