# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset for the RagedUnicorn `docker-*` image repositories.

## Usage

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>RagedUnicorn/renovate-config"
  ]
}
```

Add repo-specific `packageRules` and `customManagers` next to the `extends` line. Both are merged with the preset, not replaced.

## What the preset provides

- `config:recommended`, a weekly Monday schedule (Europe/Zurich) and the `dependencies` / `renovate` labels
- `ignorePaths` for the compose files and `examples/**`. This replaces the `:ignoreModulesAndTests` default so `test/` stays visible
- Supply-chain pinning: GitHub Actions pinned to full commit SHAs with a version comment (`helpers:pinGitHubActionDigests`) and the `Dockerfile` `FROM` base images pinned to their digest (scoped to the `dockerfile` manager, so the label and metadata test keep the plain tag)
- One `alpine` group covering the `FROM` line, the `base.name` OCI label and the metadata test, with automerge for minor and patch updates after a three day release age
- Custom managers for:
  - `# renovate: datasource=... depName=... [versioning=...]` comments before `ARG` lines in the `Dockerfile`
  - the `CONTAINER_STRUCTURE_TEST_VERSION` harness pin in `.env`
  - the Alpine `image.base.name` OCI label in the `Dockerfile`
  - the Alpine version in `test/<tool>_metadata_test.yml`

## What stays in each repo

Tool-specific rules, for example stripping the `v` prefix from GitHub release tags:

```json
{
  "packageRules": [
    {
      "description": "Strip the leading 'v' from busted GitHub release tags",
      "matchDatasources": [
        "github-releases"
      ],
      "matchPackageNames": [
        "lunarmodules/busted"
      ],
      "extractVersion": "^v(?<version>.*)$"
    }
  ]
}
```

## Validation

```sh
npx --yes --package renovate -- renovate-config-validator default.json
```
