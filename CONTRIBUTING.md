# Contributing to Zivana Labs

Thank you for your interest in contributing to Zivana Protocol and its 
ecosystem. This document applies to all repositories under the 
[zivana-labs](https://github.com/zivana-labs) organisation.

---

## Before You Contribute

Zivana Protocol is in active early development — Phase 0 and Phase 1 
of the Master Build Plan. During this period, the core team is 
establishing the foundational architecture. External contributions are 
welcome but please open an issue first before starting any significant 
work, so we can confirm the direction aligns with the build plan.

Read the Master Build Plan before contributing to any protocol 
repository. It is the authoritative reference for every technical 
decision in this project.

---

## Code of Conduct

Zivana Labs is built on the principle that trust belongs to the people 
who earn it. That principle applies inside this community too.

We are committed to a welcoming environment for contributors regardless 
of background, experience level, nationality, or technical stack. 
Disrespectful behaviour, gatekeeping, or any form of harassment will 
result in removal from the project.

If you witness or experience a conduct issue, contact the core team 
directly.

---

## How to Contribute

### Reporting a Bug

Open an issue in the relevant repository using the `bug` label. Include:

- What you expected to happen
- What actually happened
- Steps to reproduce
- Environment details (OS, Node version, Aiken version, etc.)

### Suggesting an Improvement

Open an issue using the `good-first-issue` or relevant phase label. 
Describe the problem you are solving, not just the solution you have 
in mind. The core team will respond before any implementation begins.

### Submitting Code

1. Fork the repository or create a feature branch from `develop`
2. Name your branch descriptively — use prefixes:
   - `feature/` for new functionality
   - `fix/` for bug fixes
   - `docs/` for documentation changes
   - `chore/` for maintenance tasks
3. Make your changes
4. Write or update tests where applicable
5. Open a pull request targeting `develop` — never `main`
6. Fill in the pull request description completely
7. Wait for review before merging

### Pull Request Requirements

- Target branch must be `develop`
- At least one approval from a core team member is required
- All status checks must pass once CI is configured
- No direct commits to `main` or `develop`

---

## Repository-Specific Notes

| Repository | Language | Key Requirement |
|---|---|---|
| zivana-core | Aiken | All validators must have a full test suite |
| zivana-midnight | Compact | No AI-generated code — all contracts hand-written against official Midnight documentation |
| zivana-identity | TypeScript | W3C DID spec compliance required |
| zivana-oracle | TypeScript | All attestation schemas must follow Orcfax COOP format |
| zivana-sdk | TypeScript | 85% test coverage minimum — this is the developer-facing API |
| zivana-governance | Aiken | Regenerative constraints are protocol invariants — PRs that would undermine them will be rejected |
| zivana-token | Aiken | Minting policy changes require core team supermajority approval |
| zivana-docs | Markdown | Accuracy over completeness — do not document things that do not exist yet |
| catalyst | TypeScript | Follow existing Catalyst code style and API conventions |

---

## Commit Message Convention

All commits follow this format:

type: short description in sentence case
Optional longer explanation if needed.

Valid types:

- `feat` — new feature
- `fix` — bug fix
- `docs` — documentation only
- `chore` — maintenance, dependencies, config
- `test` — adding or updating tests
- `refactor` — code change that is neither a fix nor a feature

Examples:
feat: implement covenant creation flow in Aiken validator
fix: correct trust score weighting for agent vouching events
docs: add oracle attestation schema reference to zivana-docs

---

## Licensing

All code contributed to Zivana Labs repositories is licensed under the 
MIT licence unless the repository specifies otherwise. By submitting a 
pull request, you confirm that you have the right to submit the work 
under that licence.

---

*Built for Africans. Open to the world.*
