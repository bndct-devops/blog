---
date: '2025-05-31T19:26:39+02:00'
draft: false
title: 'Part 2: Trash Guide Goodness in my Unraid Server'
description: "I mean, what else would you build a home server for?"
tags: ["homelab", "unraid", "docker", "plex", "arr"]
categories: ["Homelab", "Unraid", "Docker", "Plex", "Arr"]
author: ["benedict"]
showToc: true
TocOpen: false
cover:
  image: "https://blog.bndct.dev/images/cover_trash_guide.png"
---

> **TL;DR:** Don't do anything illegal. Jump to the [Totally Legal Use Cases](#totally-legal-use-cases) section for all the cool legal use-cases.

## Intro

In [Part 1](https://blog.bndct.dev/posts/homelab/building-unraid-server/), I walked through building my Unraid server — migrating from a mini-PC and laying the hardware foundation for a reliable homelab. The goal: build a flexible, automated media server that runs cleanly and requires minimal manual intervention.

In this post, I’ll cover how I automated everything using `Docker`, [Trash Guides](https://trash-guides.info), and a suite of containers — including **Plex**, **Jellyfin**, **Sonarr**, **Radarr**, and more — all working together to provide a seamless media experience.

---

## Why Media Automation?

Manual downloading, sorting, and renaming gets old fast.

By combining **Torrent clients**, **indexer managers**, and **media managers** into a single pipeline, I’ve built a system where content shows up automatically — properly named, sorted, and ready to watch on Plex or Jellyfin.

Trash Guides provides the logic and tuning needed to make this setup **opinionated, fast, and accurate**, prioritizing good-quality releases and reducing duplicates or mismatches.

## Docker Stack Overview

All of this runs in Docker on Unraid, using community templates and pinned images for stability. Here's what powers my media automation:

### 🧱 Core Automation Stack (Binhex)

- **`binhex-qbittorrentvpn`**  
  A torrent client with built-in VPN support — all downloads route through my ProtonVPN for safety and privacy.

- **`binhex-sonarr`**  
  Watches for new episodes of TV shows, sends them to the torrent client, and processes downloads automatically.

- **`binhex-radarr`**  
  Same as Sonarr, but for movies — with support for quality profiles like 1080p WebDL, Remux, or 4K.

- **`binhex-prowlarr`**  
  Centralizes and manages all indexers for Sonarr and Radarr.

- **`binhex-flaresolverr`**  
  Required for solving Cloudflare challenges, especially on public indexers.

### Library Frontends & Stats

- **`binhex-plexpass`**  
  My go-to for streaming — I do like FOSS but especially on my Apple TV at home, Plex has imo the best user experience.

- **`jellyfin`**  
  Used alongside Plex for a FOSS, local-first experience. Not a fan for running on Apple TV but super useful for sharing my media with friends outside my network via [Tailscale](https://tailscale.com/).

- **`tautulli`**  
  Tracks Plex usage: who’s watching, how often, and from where. I mostly use it to monitor playback quality if for some god forsaken reason my streams ever start stuttering.

- **`jellystat`**  
  Tautulli, but for Jellyfin.

### Request Management

- **`jellyseerr`**  
  My preferred front-end for managing media requests. Connected to Plex/Jellyfin and Radarr/Sonarr, it allows my users to request shows or movies, and everything else happens automatically behind the scenes.

![Automation](images/trash-guide.png)

---

## Folder Structure & Unraid Shares

Here's how I keep my data organized inside Unraid. All Docker containers map to a central share called `data`, with subfolders for torrents and media.

```zsh
/mnt/user/data/
├── torrents/
├── media/
│   ├── movies/
│   ├── tv/
│   └── etc/
```

- **`torrents/`** is where `qBittorrentVPN` downloads files.
- **`media/`** is where `Sonarr` and `Radarr` move and rename content post-download. `Plex` and `Jellyfin` point here.
- Mount paths are consistent across containers (e.g., `/data/torrents`, `/data/media`) to avoid path mismatches or hardcoding.

> Pro tip: Keeping everything under a single `data` share makes backups and parity protection easier on Unraid.

---

## Trash Guides: Why I Follow Them

Trash Guides is the foundation of this setup. Their documentation walks through best practices for:

- **Custom Formats** – Prefer proper scene releases, HDR versions, or group-specific uploads.
- **Quality Definitions** – Define what qualifies as a “good” 1080p or 4K file.
- **Release Profiles** – Filter out re-encodes or low-bitrate junk.
- **Indexer setup** – Recommendations for tracker hygiene and `FlareSolverr` integration.

---

## How It All Flows

1. **Jellyseerr** (or me) requests a movie/show.
2. **Sonarr/Radarr** queue the release via **Prowlarr** indexers.
3. **qBittorrentVPN** downloads it securely.
4. **Sonarr/Radarr** process, rename, and move it to `/data/media` subfolder.
5. **Plex and Jellyfin** scan the folders and update libraries.
6. **Tautulli and Jellystat** track usage and provide insights.

All I do is add a show to Jellyseerr. Everything else is automated.

---

## Totally Legal Use Cases

Now, before you get any ideas — this whole setup is, of course, intended *purely* for legal and legitimate purposes. Here are some 100% definitely-common use cases for an automated torrent-fed media server:

- **Archiving your personal DVD and Blu-ray collection**  
  Because...why not?

- **Downloading open-source Linux ISOs**  
  You never know when you'll need 27 different versions of Ubuntu, organized by quality, release group, and codec.

- **Curating public domain noir films from 1946**  
  Trash-guides-powered `Sonarr` + `Prowlarr` combo to auto-grab *D.O.A.* and *The Hitch-Hiker*? Yes please.

- **Creative Commons indie documentaries**  
  Okay maybe not that one.

> This guide obviously does **not** condone using any of this technology for acquiring content you don't have the rights to. That would be *wrong*. And illegal. And not I wanted to teach you about.
