---
date: '2025-06-08T12:38:23+02:00'
draft: false
title: 'Renovate - Dependabot, but cooler'
tags: ["devops", "github", "gha", "renovate", "automation"]
categories: ["Devops", "GitHub", "GHA", "Renovate", "Automation"]
author: ["benedict"]
showToc: true
TocOpen: false
cover:
  image: "https://blog.bndct.dev/images/cover_renovate.svg"
---

## Automatically Updating Versions in GitHub Actions Using Renovate

Everyone starts of by never pinning their versions, relying on `:latest` till the day some dependency pops up and fucks whatever you are deploying. Afterwards, you start following best practices but encounter why so many people follow bad practices - like `:latest`. Manually updating dependencies is a pain in the ass.

Usually `Dependabot` does the job. And it would probably do here as well. But a while ago I started using `Renovate` at work and I became quiet a fan.

Here’s how I wired Renovate to keep my Hugo version up-to-date in a GitHub Pages workflow for my [Hugo-powered blog](https://github.com/bndct-devops/blog).

---
## Problem
My GitHub Actions deployment workflow pinned the `HUGO_VERSION` to a specific release for reproducible builds. But Hugo releases fast — and checking for updates manually became a chore.

So let's automate it.

---
## The Goal
- Automatically detect new [Hugo Extended](https://github.com/gohugoio/hugo/releases) releases  
- Open a pull request that updates `HUGO_VERSION` in my workflow yaml

---

## Renovate Setup

First, let's create a `renovate.json` file in the root of the repo:

```json
{
  "extends": [
    "config:recommended"
  ],
  "automerge": false,
  "prConcurrentLimit": 5,
  "gitAuthor": "renovate[bot] <renovate[bot]@users.noreply.github.com>",
  "customManagers": [
    {
      "customType": "regex",
      "managerFilePatterns": [
        "/^\\.github/workflows/[^/]+\\.ya?ml$/"
      ],
      "matchStrings": [
        "HUGO_VERSION:\\s*(?<currentValue>\\d+\\.\\d+\\.\\d+)"
      ],
      "depNameTemplate": "gohugoio/hugo",
      "datasourceTemplate": "github-releases"
    }
  ]
}
```

### How It Works

- `customManagers`: Scans GitHub Actions YAML files for HUGO_VERSION using a regular expression

- `datasourceTemplate`: Pulls new versions from GitHub Releases (gohugoio/hugo)

- `depNameTemplate`: Used to label the update PRs properly

When a new release like v0.147.9 drops, Renovate opens a PR to update this line:

```yaml
env:
  HUGO_VERSION: 0.147.8
```

### Running Renovate with GitHub Actions

You could run Renovate's hosted app. But I kinda like that approach more - if I can selfhost it, I do. There would be the option to actually run a Renovate Server. But that would be overkill for my lil repo I have here.

```yaml
.github/workflows/renovate.yaml

name: Renovate

on:
  schedule:
    - cron: '0 3 * * *'  # Run daily
  workflow_dispatch:

jobs:
  renovate:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
      issues: write
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '22'

      - run: npm install -g renovate

      - uses: actions/create-github-app-token@v2
        id: app-token
        with:
          app-id: ${{ secrets.RENOVATE_APP_ID }}
          private-key: ${{ secrets.RENOVATE_PRIVATE_KEY }}

      - run: renovate --autodiscover
        env:
          RENOVATE_TOKEN: ${{ steps.app-token.outputs.token }}
          RENOVATE_PLATFORM: github
          RENOVATE_REPOSITORIES: ${{ github.repository }}
          LOG_LEVEL: debug
```
### How to authenticate
If you store your versions in a .version file, the default `GitHub Token` provided by GitHub Actions should do the trick. But I don't, so I went with an alternative method. 

You can either go with a `PAT` (Personal Access Token) - which I am personally not a fan of. Or a GitHub App.
One of the benefits of a GitHub App is, that it basically let's you create `PAT's`, but makes them short-lived. It's not neccessary, but I like that approach and it's a best practice in my opinion.

To create a GitHub App, follow the official documentation: https://docs.github.com/en/apps/creating-github-apps

Once that's done, generate a `PRIVATE_KEY` and save it as a GitHub Actions secret. The `APP_ID` does not need to be a secret, but I do it anyway. Now you can create `PAT's` with the `actions/create-github-app-token` action.

## Hugo Deployment Workflow
The deployment workflow uses the version Renovate manages:

```yaml
env:
  HUGO_VERSION: 0.147.8
```

Then installs Hugo like this:

```zsh
wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
&& sudo dpkg -i ${{ runner.temp }}/hugo.deb
```

Because the version is hardcoded, builds are repeatable — and thanks to Renovate, always current.

---
## The Result

Whenever a new Hugo version is released:

- Renovate opens a PR like chore(deps): update hugo to v0.147.9
- The PR changes only the version number in the workflow YAML
- I review, merge and GitHub deploys automatically with the new Hugo Version
