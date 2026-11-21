# .github

Shared GitHub Actions workflows for the repositories of this account.

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

jobs:
  codacy:
    name: Codacy
    permissions:
      contents: read
      security-events: write
      actions: read
    uses: mordilloSan/.github/.github/workflows/codacy-scan.yml@main
```

The check appears on pull requests as `Codacy / Codacy Security Scan`.

To run other tools or versions in one repository, add `.codacy/codacy.yaml` to
it; the shared workflow then leaves it as it is.

Callers follow `@main`: a change merged here reaches every repository on its
next run, without a pull request there.
