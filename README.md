# Automatic Versioning with Semantic Release 

**Semantic Versioning (SemVer)** is a versioning scheme for software that uses a three-part number format: `MAJOR.MINOR.PATCH` (e.g., `1.2.3`). This approach helps developers and users understand the impact of new releases at a glance. GitLab, as a popular CI/CD platform, integrates well with semantic versioning, making it easy to manage and automate version releases.

The format for SemVer is: 

- **MAJOR**: Increased when you make incompatible API changes.
- **MINOR**: Increased when you add functionality in a backward-compatible manner.
- **PATCH**: Increased when you make backward-compatible bug fixes.

For example, `1.0.0` might be the initial release, and future releases could look like:
- `1.1.0` for a minor, backward-compatible feature addition.
- `1.1.1` for a bug fix that doesn't affect the API.
- `2.0.0` for breaking changes.


## Table of Contents

- [Features](#features)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Versioning](#versioning)


## Features

- **Automated Versioning:** Uses [Semantic Release](https://semantic-release.gitbook.io/) to manage versioning and changelog generation.
- **Consistent Commit Style:** Enforces [Conventional Commits](https://www.conventionalcommits.org/) to maintain a clean and understandable commit history.
- **Automated Changelogs:** Generates changelogs based on commit messages.

## Getting Started

These instructions will help you set up the project and use Semantic Release with Conventional Commits.

### Prerequisites

- Basic understanding of Git


## GitHub - Automatic versioning

1. Create `.github/worflows/release.yml` file in a git repository

```yaml
name: Release
"on":
  push:
    branches:
      - main
permissions:
  contents: read # for checkout
jobs:
  release:
    permissions:
      contents: write # to be able to publish a GitHub release
      issues: write # to be able to comment on released issues
      pull-requests: write # to be able to comment on released pull requests
      id-token: write # to enable use of OIDC for npm provenance
    name: release
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@eef61447b9ff4aafe5dcd4e0bbf5d482be7e7871 # v4.2.1
      - uses: actions/setup-node@0a44ba7841725637a19e28fa30b79a866c81b0a6 # v4.0.4
        with:
          cache: npm
          node-version: lts/*
      - run: npm install -g semantic-release @semantic-release/github @semantic-release/changelog conventional-changelog-conventionalcommits @semantic-release/commit-analyzer @semantic-release/git
      - run: semantic-release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          #NPM_TOKEN: ${{ secrets.SEMANTIC_RELEASE_BOT_NPM_TOKEN }}
```

> At the start of each workflow job, GitHub automatically creates a unique GITHUB_TOKEN secret to use in your workflow. You can use the GITHUB_TOKEN to authenticate in the workflow job.

If you still want to create a GITHUB_TOKEN

2. Create a Personal Access Token (classic)

   1. Settings -> Developer Settings -> Personal access tokens (classic) named `GITUB_TOKEN` 

   > When creating the token, the minimum required scopes are:
   `repo` for a private repository
   `public_repo` for a public repository
 
   2. Add secret to Project Setting 


3. Create a file named `.releaserc` and paste the following code:

```json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    [
      "@semantic-release/changelog",
      {
        "changelogFile": "CHANGELOG.md"
      }
    ],
    [
      "@semantic-release/git",
      {
        "assets": ["CHANGELOG.md"]
      }
    ],
    [
      "@semantic-release/github",
      {
        "githubUrl": "https://github.com"
      }
    ]
  ]
}
```

**NOTE.** In `.releaserc` the order is important in order for plugins to work

✅ After running the pipeline a new git tag and a `CHANGELOG.md` file have been created !
