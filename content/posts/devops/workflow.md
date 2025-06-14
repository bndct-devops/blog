---
date: '2025-06-13T19:36:54+02:00'
draft: false
title: 'My Homelab Workflow'
tags: ["devops", "karakeep", "obsidian", "kanban", "workflow"]
categories: ["Devops", "Karakeep", "Obsidian", "Kanban", "Workflow"]
author: ["benedict"]
showToc: true
TocOpen: false
cover:
  image: "https://blog.bndct.dev/images/cover_workflow.png"
---

Took some while, but I’ve settled into a workflow that balances flexibility with structure (and actually works for me). It covers the whole process — from capturing ideas on the go to publishing posts on this blog.

This isn’t a deep dive into any one tool, but more of an overview of how everything fits together.

## Step 1: Hoard Ideas in Karakeep

Whenever I see something that looks cool (usually while tinkering with my homelab, checking Reddit or running into a problem and searching for a solution), I put it into Karakeep. Usually I don't even read through them at first - filtering comes later.  

### Karakeep
> On that note, `Karakeep` is pretty cool. I have seen `Hoarder` mentioned a lot recently (because it got shut down and replaced by `Karakeep` which made a lot of people angry). Initially I didn't see the need for a bookmarking app. Especially after seeing the stack needed to run it - if you are the same, give [Linkding](https://github.com/sissbruecker/linkding) a try - but after using it for a while it is quite nice. It replaced [Obsidians Web Clipper](https://obsidian.md/clipper) for me.

## Step 2: Move Ideas into Obsidian

### Trying stuff out
Once in a while I check `Karakeep` and move worthy ones I wanna try out into my Obsidian vault. I use the `Kanban` plugin to track progress.

The board lives at `homelab/!board.md` and has three columns:

- `Planned` — Things I wanna try out
- `Testing` — Things I currently run on my server
- `Documented` — Things I'll keep and documented (basically my done column)

Each card links to an Obsidian note—usually named like `service-<topic>.md`.

![Kanban](images/service_kanban.png)

### Sharing stuff
If I am happy with whatever I just set up, ran into problems or just think it's cool but not that well known, I'll write a little post for it.

It's the same process as above. Got a second board for my blog: `blog/!board.md`

- `Planned` — Things I could write about
- `Todo` — Things I decided to write about
- `InProgress` — Things I am writing about (this one right now - Friday 13th at 10:29 PM)
- `Done` - Things that are published


### Short intermission - my Obsidian Vault

I try to keep my `Obsidian` vault flat. I've started with subfolders f.e. homelab -> service -> security -> vaultwarden - but I personally didn't see any benefits. It just made navigating a pain in the ass. But no folders also does not work for me.

Example structure:

```zsh
├── blog
│   ├── !board.md
│   ├── blog-karakeep.md
│   └── blog-renovate.md
├── excalidraw
├── notes
├── homelab
│   ├── !board.md
│   └── service-vaultwarden.md
├── templates
│   ├── template-service_template.md
│   └── template-blog_content_template.md
```

I rely on a strong naming convention (`blog-`, `service-`, `clip-`, etc) and `backlinks`/`tags` to keep everything discoverable and organized.

![Obsidian](images/obsidian.png)

## Step 3: Writing

Once I've got time (and feel bad that I haven't posted anything in a while) I pick one of my `ToDo` cards and start thinking about how to structure it. Me, being an idiot myself, try my best to make my posts as easy to follow as possible.

At the moment I have 2 big topics on `blog.bndct.dev`:

- homelab
- devops

Depending on where I want the post to go into, I either one of the following in the root of my blog repo:
- `hnpd $BLOG_NAME.md`
- `hnph $BLOG_NAME.md`

```zsh
# Create a new DevOps post
# Usage: hndp my-new-devops-article.md
hnpd() {
  hugo new "content/posts/devops/$1"
}

# Create a new Homelab post
# Usage: hnhl my-cool-homelab-project.md
hnph() {
  hugo new "content/posts/homelab/$1"
}
```

Writing in markdown keeps things clean and portable. I version everything with Git, do my diagram and graphics in [Excalidraw](https://blog.bndct.dev/posts/homelab/excalidraw/) and use [GitHub Actions](https://blog.bndct.dev/posts/devops/setup_hugo_github/) to deploy automatically on push.

## Why This Workflow Works (atleast for me)

- Quick, no effort idea capture via `Karakeep`  
- Clear visual planning with `Obsidian Kanban`
- A flat, grep-friendly vault that’s easy to search and maintain  
- Markdown-first writing flow using `VSCode`  
- `Git` + `GitHub Actions/Pages` for painless publishing  
