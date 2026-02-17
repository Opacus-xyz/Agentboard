# Contributing Guide

Thanks for contributing to Agentboard.

## Principles

- Keep changes focused and minimal
- Preserve production behavior unless intentionally changing it
- Prefer root-cause fixes over surface-level patches
- Avoid unrelated refactors in feature/fix PRs

## Branch and Commit Style

- Use clear branch names:
  - feat/<short-topic>
  - fix/<short-topic>
  - docs/<short-topic>
- Commit style:
  - feat: ...
  - fix: ...
  - docs: ...
  - chore: ...

## Pull Request Checklist

- Explain what changed and why
- Include before/after behavior summary
- Include manual test steps
- Confirm no secrets are committed
- Confirm docs updated when behavior changes

## Testing Expectations

For UI changes:

- Verify login flows
- Verify agent create/list/delete
- Verify dashboard metrics render
- Verify no blocking modal remains after success/error

For backend-dependent changes:

- Verify /health
- Verify /auth/config
- Verify /api/agents smoke flow

## Security and Secrets

- Never commit private keys
- Never commit OAuth secrets
- Do not paste full sensitive env values into issues/PRs

## Documentation Rule

If behavior changes, update at least one of:

- README.md
- QUICKSTART.md
- CHANGELOG.md
- ROADMAP.md
