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
- Automerges all updates, majors included, with squash, and labels them with `renovate`
- Rebases PRs when they fall behind the base branch
- Opens up to 10 PRs per hour
- Runs lock file maintenance
- Lets npm fetch remote tarballs when it updates lock files. npm 12 refuses registry tarballs of
  packages with `bundleDependencies` (e.g. `aws-cdk-lib`) with `EALLOWREMOTE`
  ([npm/cli#9800](https://github.com/npm/cli/issues/9800))
- Scans test and example directories too, only `node_modules`, `bower_components` and `vendor` are ignored
- Pins npm dependencies to exact versions, bumps Cargo.toml ranges
- Groups npm lint tooling (eslint, typescript-eslint, prettier, eslint plugins and configs) into one PR, because of their peer dependencies
- Groups rubocop and its extensions into one PR

Repos only add settings in their own `renovate.json` when there is a reason, documented in a `description`.
