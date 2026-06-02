# 🐍 python-template

[![Ruff](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/ruff/main/assets/badge/v2.json)](https://github.com/astral-sh/ruff)
[![uv](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/uv/main/assets/badge/v0.json)](https://github.com/astral-sh/uv)
[![Copier](https://img.shields.io/badge/made%20with-copier-blueviolet)](https://copier.readthedocs.io)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

A Python project template. Generate a new project with CI/CD, linting, type checking, security scanning, and automated semantic releases.

---

## What's included

| Tool | Purpose |
|---|---|
| [uv](https://github.com/astral-sh/uv) | Dependency management and virtual environments |
| [Hatchling](https://hatch.pypa.io) | PEP 517 build backend |
| [Ruff](https://github.com/astral-sh/ruff) | Linting and formatting |
| [ty](https://github.com/astral-sh/ty) | Type checking |
| [Bandit](https://github.com/PyCQA/bandit) | Security scanning |
| [pre-commit](https://pre-commit.com) | Git hooks (ruff, ty, bandit, hygiene) |
| [pytest](https://pytest.org) | Testing with coverage |
| [GitHub Actions](https://github.com/features/actions) | CI across multiple Python versions |
| [python-semantic-release](https://python-semantic-release.readthedocs.io) | Automated versioning, changelog, and release PRs |

---

## Requirements

- Python 3.11+
- [uv](https://docs.astral.sh/uv/getting-started/installation/)
- [Copier](https://copier.readthedocs.io/en/stable/) — install once via `uv tool install copier`

---

## Usage

```bash
copier copy gh:your-username/python-template my-new-project
```

Copier will ask a few questions:

| Question | Example |
|---|---|
| Project name | `My Awesome Library` |
| Project slug | `my_awesome_library` |
| Description | `A library that does awesome things` |
| Author name | `Jane Smith` |
| Author email | `jane@example.com` |
| GitHub username | `janesmith` |
| Python version | `3.12` |
| License | `MIT` |

Then bootstrap the generated project:

```bash
cd my-new-project

git init
uv sync
uv run pre-commit install

git add -A
git commit -m "feat: initial commit"

gh repo create my-new-project --public --source=. --push

# Required for the release workflow — see Releases section below
gh secret set GH_TOKEN --body "ghp_yourPersonalAccessToken"
```

---

## CI

Every push and pull request runs:

- **Ruff** — lint and format check
- **ty** — type checking
- **Bandit** — security scan
- **pre-commit** — all hooks
- **pytest** — tests with coverage across multiple Python versions

---

## Releases

Releases are fully automated using [Conventional Commits](https://www.conventionalcommits.org/).

| Commit prefix | Version bump |
|---|---|
| `feat:` | minor — `0.x.0` |
| `fix:`, `perf:` | patch — `0.0.x` |
| `feat!:` or `BREAKING CHANGE:` footer | major — `x.0.0` |

On every merge into `main`, the release workflow will:

1. Compute the next version from commit history
2. Create a `release/vX.Y.Z` branch with the version bump and updated `CHANGELOG.md`
3. Open a PR into `main` titled `chore(release): vX.Y.Z`
4. Wait for your approval, CI runs on the PR and the merge button is gated on passing checks and your review
5. On merge: tag the commit and create a GitHub Release

### Publishing to PyPI (optional)

The publish job is gated by a GitHub Environment named `pypi` and disabled by default via `if: false`. To enable it:

1. Create an Environment named `pypi` under **Settings → Environments** and add required reviewers.
2. Configure [PyPI Trusted Publishing](https://docs.pypi.org/trusted-publishers/) for the repo (the workflow already requests `id-token: write`).
3. Remove the `if: false` line from the `Publish to PyPI` step in `.github/workflows/release.yml`.

### GH_TOKEN

The release workflow opens PRs and merging the release PR re-triggers CI. You need a [Personal Access Token](https://github.com/settings/tokens?type=beta) with these permissions on the target repo:

- Contents: Read and write
- Pull requests: Read and write
- Workflows: Read and write

Set it on each generated repo:

```bash
gh secret set GH_TOKEN --body "ghp_yourToken" --repo your-username/my-new-project
```

---

## Updating a generated project

If you update this template later, you can pull the changes into any project generated from it:

```bash
cd my-new-project
copier update
```

Copier will show a diff of what changed and let you accept or reject each change.

---

## License

MIT