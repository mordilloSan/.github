# .github

Shared GitHub Actions workflows and default community health files for the
repositories of this account.

## Default community health files

GitHub shows these files in every public repository of the account that has
no file of the same kind of its own:

| File | Purpose |
|---|---|
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | how to report, branch, commit and open a pull request |
| [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md) | Contributor Covenant 2.0 |
| [`.github/ISSUE_TEMPLATE/`](.github/ISSUE_TEMPLATE) | bug and feature forms; blank issues are off |

A repository's own file of the same kind replaces the default entirely. For
issue templates, any `.github/ISSUE_TEMPLATE/` folder in the repository
replaces the whole default folder: fan_control keeps its own forms for that
reason.

`SECURITY.md` stays in each repository, since supported versions differ.
Three files cannot be shared from here and live in each repository:
`.github/CODEOWNERS`, `.github/dependabot.yml` and the workflows that call the
shared ones.

Every repository's `CODEOWNERS` names the maintainer, but its `main` ruleset
does not require a code-owner review yet: GitHub never lets the author of a
pull request approve it, so with one maintainer the rule would block every
pull request the maintainer opens, release pull requests included. When a
second maintainer joins, add them to each `CODEOWNERS` and turn on **Require
review from Code Owners** in each repository's `main` ruleset.

## Dependabot

Every repository's `.github/dependabot.yml` has the same shape, starting with
this block for its workflows:

```yaml
version: 2
updates:
  - package-ecosystem: github-actions
    directory: /
    schedule:
      interval: weekly
    cooldown:
      default-days: 7
    groups:
      actions:
        patterns: ["*"]
    commit-message:
      prefix: ci
      include: scope
```

A repository with package manifests adds one block per ecosystem (`gomod`,
`npm`, `docker`) with the same schedule and cooldown, a group of
`update-types: [minor, patch]` so a new major version arrives in a pull
request of its own, and `prefix: build`. The prefixes put the updates in the
CI/CD and Build sections of the gh-release-flow release notes. The seven-day
cooldown keeps a new release, including a hijacked one, out for a week;
security updates skip it.

## Codacy scan

[`.github/workflows/codacy-scan.yml`](.github/workflows/codacy-scan.yml) runs
opengrep and trivy through Codacy CLI v2 and uploads the results to the calling
repository's code scanning (Security tab). The CLI version, its SHA256 and the
default tool list are kept in that one file.

A repository uses it with this `.github/workflows/codacy.yml`:

```yaml
name: Codacy Security Scan

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: ${{ github.event_name == 'pull_request' }}

jobs:
  codacy:
    name: Codacy
    permissions:
      contents: read
      security-events: write
      actions: read
    uses: mordilloSan/.github/.github/workflows/codacy-scan.yml@<commit of the release> # vX.Y.Z
```

The check appears on pull requests as `Codacy / Codacy Security Scan`.

To run other tools or versions in one repository, add `.codacy/codacy.yaml` to
it; the shared workflow then leaves it as it is.

Callers pin a release of this repository by its commit, with the version in
the comment, like any other action. A change merged here reaches a repository
only after it is released and that repository's weekly Dependabot pull request
(`ci(deps)`, the `actions` group) moves the pin, so the change is tested there
before it applies.

## Releases

A release is always made from a `dev/vX.Y.Z` branch: merging its pull request
into `main` starts [`release.yml`](.github/workflows/release.yml), which
requires the pull request's other checks to have passed and publishes the
GitHub Release for that tag, with the pull request body as its notes. The
version is for the shared workflows: a change to their inputs, permissions or
check name that callers must follow is a major version.

## Defaults for new repositories

[`defaults/`](defaults) holds what `gh release-flow init` (or `make init`)
applies to a new repository of this account. The layout is a convention of
gh-release-flow, described in its [README](https://github.com/mordilloSan/gh-release-flow#repository-defaults);
`init` only reads these files, it never runs anything from here.

- [`defaults/files/`](defaults/files): files copied into the repository at the
  same path, with `{{owner}}`, `{{repo}}` and `{{year}}` filled in. A file the
  repository already has is kept. They are the licence, the security policy,
  `CODEOWNERS`, the Dependabot block and Codacy caller above, a Check workflow
  that runs `make check` and `make test`, and a `Makefile` whose `check` and
  `test` have nothing to do yet, so the first pull request passes.
- [`defaults/settings/`](defaults/settings): one request body per GitHub API
  call, each file named after its call; an empty file is a call without a
  body, which turns that feature on. Together they set merge commits only,
  Actions that must be pinned to a full commit SHA, a read-only Actions token,
  CodeQL default setup, secret scanning with push protection, Dependabot alerts
  and security updates, private vulnerability reporting and immutable
  releases. `actions-permissions.json` needs gh-release-flow v2.2.0 or later;
  an older version reports it and skips it.
- [`defaults/rulesets/`](defaults/rulesets): the `main` and `release tags`
  rulesets, as bodies the create API accepts. A ruleset whose name the
  repository already has is kept.

The `main` ruleset requires the checks `check` (the Check workflow's job) and
`Codacy / Codacy Security Scan`; renaming either check means changing
`defaults/rulesets/main.json` with it.

To change a default, edit it here through a pull request. The change applies
to repositories set up afterwards; existing repositories are not touched.
Language-specific parts, such as a Dependabot block per package ecosystem or
CI for Go or TypeScript, are not defaults: add them to each repository by hand.
