# Contributing

Thank you for helping. These guidelines apply to every mordilloSan repository
that has no `CONTRIBUTING.md` of its own. Each repository's README says how to
build and test it.

## Before you start

- **Bugs and ideas:** open an issue with one of the forms. For anything larger
  than a small fix, open an issue first so we can agree on the approach before
  you write the code.
- **Security problems:** never in a public issue. Use **Report a
  vulnerability** on the repository's Security tab, as its `SECURITY.md`
  describes.

## Pull requests

- Branch from `main` and open the pull request against `main`. Keep each pull
  request to one change.
- Run the repository's own checks (its Makefile or README says how) before you
  push. The required CI checks must pass before anything merges.
- Pull requests merge with a merge commit, never squashed, so every commit on
  your branch is kept. Tidy them before you ask for a review.
- Releases belong to the maintainer, who cuts them with
  [gh-release-flow](https://github.com/mordilloSan/gh-release-flow) from a
  `dev/vX.Y.Z` branch. A pull request does not bump a version, edit release
  notes or push a tag.

## Commit messages

Write every commit subject as a
[Conventional Commit](https://www.conventionalcommits.org/):
`type(scope): subject`. The scope is optional and the subject is imperative,
at most 72 characters. Use `feat`, `fix`, `docs`, `style`, `refactor`, `perf`,
`test`, `build`, `ci` or `chore`. The release notes are built from these
subjects, one section per type, so write each subject for the people who will
read the release.

The body explains why. The last paragraph may hold footers, one per line, which
the release notes read:

| Footer | Meaning |
| --- | --- |
| `Fixes #12` (also `Closes`, `Resolves`) | The change fixes that issue. GitHub closes it when the commit reaches `main`, and the notes link it |
| `Refs #12` | The change relates to that issue, which stays open |
| `BREAKING CHANGE: what breaks` | Something that worked before stops working; the notes list it under Breaking Changes. A `!` after the type, as in `feat!:`, means the same |
| `Security: GHSA-xxxx-xxxx-xxxx` | The change fixes the vulnerability in that advisory |
| `Security: low`, `moderate`, `high` or `critical` | The change closes a way to misuse the software that has no advisory, at that severity |

A commit with a `Security:` footer is listed under Security, with its severity.
A vulnerability is still reported privately first, as above; the footer goes on
the fix, not in a public issue.

```text
fix(auth): check the session token

The token was compared before it was decoded, so a malformed one passed.

Security: moderate
Fixes #12
```

## Licence

By contributing, you agree that your contribution is licensed under the
repository's licence, given in its `LICENSE` file.

## Code of conduct

Everyone taking part follows the
[code of conduct](https://github.com/mordilloSan/.github/blob/main/CODE_OF_CONDUCT.md).
