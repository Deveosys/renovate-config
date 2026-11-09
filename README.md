# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) configuration, used as the default preset for our repositories.

## Usage

In the repository you want to configure, create a `renovate.json` file that extends this preset:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>Deveosys/renovate-config:renovate"]
}
```

The `:renovate` suffix tells Renovate to load this repository's `renovate.json` file. Renovate always reads from the default branch.

To pin a version of the preset, add a Git tag: `github>Deveosys/renovate-config:renovate#v1.0.0`.

> **Note:** without a suffix (`github>Deveosys/renovate-config`), Renovate looks for a `default.json` file. Renaming `renovate.json` to `default.json` would allow the shorter form. The Renovate docs also discourage using `renovate.json` as a preset file name.

## Behavior

### Included presets

| Preset | Effect |
| --- | --- |
| `config:recommended` | Renovate's recommended baseline. |
| `group:allNonMajor` | Groups all minor and patch updates into a single PR ("all non-major dependencies"). |
| `:dependencyDashboard` | Creates a "Dependency Dashboard" issue listing all pending updates. |

### Global options

| Option | Value | Effect |
| --- | --- | --- |
| `minimumReleaseAge` | `3 days` | A version must have been published for at least 3 days before it is proposed. This lowers the risk of picking up a broken or compromised release. |
| `rebaseWhen` | `conflicted` | Branches are rebased only when they have conflicts, not on every commit to the base branch. |
| `prConcurrentLimit` | `5` | At most 5 Renovate PRs open at the same time. |
| `platformAutomerge` | `true` | Automerge uses the platform's native feature (GitHub auto-merge, for example). |
| `lockFileMaintenance` | enabled | Regenerates lock files weekly (default schedule: `before 4am on monday`) to update transitive dependencies. |

### Update rules

| Update type | Behavior |
| --- | --- |
| Minor / patch of `devDependencies` | Automerged directly on the branch, without a PR, if checks pass. Renovate opens a PR only if tests fail or stay pending too long. |
| Minor / patch (other dependencies) | Automerged through a PR once checks are green. |
| Major | Nothing is created automatically: approve the update by ticking its checkbox in the Dependency Dashboard. |

> Automerge requires the target repository's CI to pass. Without tests, Renovate merges nothing, unless the repository adds `"ignoreTests": true`.

## Overriding the preset

A repository can redefine any option after `extends`:

```json
{
  "extends": ["github>Deveosys/renovate-config:renovate"],
  "prConcurrentLimit": 10,
  "packageRules": [
    {
      "matchPackageNames": ["typescript"],
      "automerge": false
    }
  ]
}
```

The repository's `packageRules` are appended to the preset's. Because they are evaluated afterwards, they win in case of conflict.

## Changing this preset

Any change on the default branch applies on the next run to **every repository** that extends this preset without a tag. Validate the file before pushing:

```bash
npx --yes --package renovate -- renovate-config-validator renovate.json
```

## References

- [Shareable Config Presets](https://docs.renovatebot.com/config-presets/)
- [Configuration options](https://docs.renovatebot.com/configuration-options/)
- [`group:*` presets](https://docs.renovatebot.com/presets-group/)
