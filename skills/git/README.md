# Skill: Git

## Purpose
Keep git history clean, readable, and useful for future contributors.

## Commit Messages
Format: `<type>(<scope>): <short summary>`

| Type | When to use |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | Code change with no behavior change |
| `docs` | Documentation only |
| `test` | Adding or updating tests |
| `chore` | Tooling, dependencies, config |

- Summary: imperative mood, max 72 chars, no period
- Body (optional): explain *why*, not *what*

## Branching
- `main` / `master`: always deployable
- Feature branches: `feat/<short-description>`
- Fix branches: `fix/<short-description>`

## Pull Requests
- One logical change per PR
- Self-review before requesting review
- Link related issues in the PR description

## Rules
- Never force-push to `main`
- No commented-out code in commits
- Squash WIP commits before merging
