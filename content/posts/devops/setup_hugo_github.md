---
date: '2025-05-16T17:09:59+02:00'
draft: false
title: 'How to Set Up Hugo and Deploy via GitHub Actions'
description: "Static site generators like Hugo make it simple to build fast websites. In this post, we'll set up a Hugo site and configure GitHub Actions to automatically deploy it to GitHub Pages whenever you push changes."
tags: ["homelab", "devops", "ci", "ci/cd", "hugo", "github"]
categories: ["Homelab", "DevOps", "CI/CD", "GitHub Pages", "GitHub Actions", "GitHub", "Hugo"]
author: ["benedict"]
showToc: true
TocOpen: false
cover:
  image: "https://blog.bndct.dev/images/cover_hugo.png"
---

### Before we start
Make sure you have:

- Go installed (for Hugo)
- Hugo installed
- A GitHub account

### Create your site
First step, open your terminal of choice, in my case that's [**iTerm2**](https://iterm2.com/) and think about what you wanna call your website. In my case and in all the following examples that will be `blog`. After running the `hugo new site` command with the `--format yaml` flag (I prefer `yaml` over `toml` whenever possible) `hugo` creates a new directory with pretty much everything you need. Change into that directory and create a new git repository.

```zsh
hugo new site blog --format yaml
cd blog
git init
```

### Choose your theme
One of the huge benefits of `Hugo` is the community support - in particular for themes. You can find a nearly unlimited amount of themes on the official `Hugo` site, but there are many more throughout the internet. I'd suggest to go with the official ones first - https://themes.gohugo.io/.

Me personally is a fan of very minimalist and simple setups, so I went for `PaperMod`. There are many ways to integrate a `Hugo` theme into your repository, but the recommended way is to just use `submodules`. If you wanna know more about them, I'd suggest visiting the [official documentation](https://git-scm.com/book/en/v2/Git-Tools-Submodules). If I go into it here, it will quadruple the length of this post.

```zsh
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
echo 'theme = "PaperMod"' >> config.yaml
```

After that's done, check if the theme was correctly added and update your `baseURL`:

```zsh
baseURL = "https://yourusername.github.io/"
languageCode = "en-us"
title = "My Hugo Blog"
theme = "PaperMod"
```

### Create your first page

It's totally up to you how you structure your `hugo` setup, I personally like to do a `content -> posts -> topic folders`. Here is a example how its roughly setup for this very blog.

```zsh
blog
├── assets
content
│   ├── about
│   ├── archives.md
│   └── posts
│       ├── devops
│       └── homelab
├── hugo.yaml
├── layouts
├── README.md
└── themes
```

Anyway, lets create our first blog. Simply follow that command below, it will follow my setup again:

```zsh
hugo new content content/posts/homelab-access-architecture.md
```

This will create a new file (new directories if they were not there yet) and prefill it with `front matter`. In short, `front matter` is metadata at the top of a content file used by `Hugo` to configure how the page is rendered.

Now edit the post in `content/posts/homelab-access-architecture.md` and set `draft: false` to make it public.

With that, you can finally take a look at your site for the first time. Just run:

```zsh
hugo server
```

and open your browser and go to http://localhost:1313

### Push Your Site to GitHub and setup GitHub Pages
Create a new GitHub repository, then push:

```zsh
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

Create a file at `.github/workflows/hugo.yml` with the following content:

```zsh
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches: [main]

workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.147.1

    steps:
    - uses: actions/checkout@v4
      with:
        submodules: recursive
        fetch-depth: 0

    - name: Install Hugo CLI
      run: |
        wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
        && sudo dpkg -i ${{ runner.temp }}/hugo.deb

    - name: Setup Pages
      id: pages
      uses: actions/configure-pages@v5

    - name: Build
      run: hugo --minify

    - name: Upload artifact
      uses: actions/upload-pages-artifact@v3
      with:
        path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
    - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

Go to your repository's **Settings > Pages** and set the source to:

    GitHub Actions

Commit your workflow file, push it to GitHub and wait a few seconds till you're page is live.