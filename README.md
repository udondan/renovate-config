# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) config for my repositories.

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>udondan/renovate-config"]
}
```

## What it does

- Extends `config:recommended`, all commits use the `chore` type
- Waits 3 days after a release before updating
- Automerges PRs with squash, labels them with `renovate`
- Rebases PRs when they fall behind the base branch
- Groups npm lint tooling (eslint, typescript-eslint, prettier, eslint plugins and configs) into one PR, because of their peer dependencies
- Groups rubocop and its extensions into one PR

Repos override settings in their own `renovate.json`.
