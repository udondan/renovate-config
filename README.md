# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) config for my repositories.

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>udondan/renovate-config"]
}
```

## What it does

- Extends `config:recommended`, updates use `chore(deps)`, runtime dependencies `build(deps)` (see [Releases](#releases))
- Runs in full mode, so repos the Mend app adds in silent mode still get PRs and the Dependency Dashboard
- Waits 3 days after a release before updating, except for own packages, actions and images
  (`@udondan/*`, `udondan/*`, `github.com/udondan/*` or a source repository under
  `github.com/udondan`), which are updated right away
- Automerges all updates, majors included, with squash, and labels them with `renovate`
- Rebases PRs when they fall behind the base branch
- Opens up to 10 PRs per hour
- Runs lock file maintenance
- Lets npm fetch remote tarballs when it updates lock files. npm 12 refuses registry tarballs of
  packages with `bundleDependencies` (e.g. `aws-cdk-lib`) with `EALLOWREMOTE`
  ([npm/cli#9800](https://github.com/npm/cli/issues/9800))
- Scans test and example directories too, only `node_modules`, `bower_components` and `vendor` are ignored
- Pins GitHub Actions to a commit SHA, except the first-party `actions/*` and `github/*` and
  `udondan/boarding-gate`, whose reusable workflow is used by its major tag (`@v1`) so every
  release reaches all repos without update PRs
- Pins npm dependencies to exact versions, bumps Cargo.toml ranges
- Groups npm lint tooling (eslint, typescript-eslint, prettier, eslint plugins and configs) into one PR, because of their peer dependencies
- Groups rubocop and its extensions into one PR

Repos only add settings in their own `renovate.json` when there is a reason, documented in a `description`.

## Releases

release-please only opens a release PR when the changelog would have an entry. Updates of dependencies that ship to users
use the `build(deps)` type, which the release-please config of the repos shows under "Dependencies". All other updates
(dev and test dependencies, CI actions, tools in `mise.toml`, lock file maintenance) use `chore(deps)`, which is hidden, so
they don't cause a release.

`build(deps)` is used for:

| Manager | File | Dependency types |
| --- | --- | --- |
| npm | root `package.json` | `dependencies`, `optionalDependencies`, `peerDependencies` |
| pep621, poetry | root `pyproject.toml` | `project.dependencies`, `project.optional-dependencies`, `dependencies`, `extras` |
| cargo | root `Cargo.toml` | `dependencies`, `workspace.dependencies` |
| swift | root `Package.swift` | all |
| gomod | root `go.mod` | `require` |
| github-actions | root `action.yml` | all |

When one PR updates a dependency in several places, Renovate uses the highest type of all of them, so a runtime update is
never hidden behind a dev update in the same PR.

Repos that ship more than that add a rule to their own `renovate.json`, e.g. a Docker image (final stage base image,
lock file maintenance) or a package in a subdirectory:

```json
{
  "description": "The Docker image ships the final stage base image and the locked dependencies.",
  "matchManagers": ["dockerfile"],
  "matchDepTypes": ["final"],
  "semanticCommitType": "build"
}
```

The matching `changelog-sections` in `release-please-config.json`:

```json
{ "type": "build", "scope": "deps", "section": "Dependencies" },
{ "type": "chore", "scope": "deps", "section": "Dependencies (dev)", "hidden": true },
```
