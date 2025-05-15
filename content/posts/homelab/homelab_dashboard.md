---
date: '2025-05-14T23:04:26+02:00'
draft: false
title: 'Homelab Dashboard'
description: "In the world of homelabbing - especially after one amasses a good amount of services - having a good dashboard is essential. "
author: ["benedict"]
showToc: true
TocOpen: false
cover:
  image: "https://blog.bndct.dev/images/cover_dashboard.png"
---

# Battle of the Dashboards: Homepage vs. Homarr vs. Heimdall

For a lot of people functionality is king. But anyone who read my Unraid Server Guide knows that a consistent design  is almost as important in my opinion. 

In the world of homelabbing, a good dashboard is more than just eye candy — it's your command center. Whether you're tracking services, monitoring uptime, or just flexing your setup, a dashboard ties it all together.

In this post, I compare three popular self-hosted dashboards: [Homepage](https://gethomepage.dev), [Homarr](https://github.com/ajnart/homarr), and [Heimdall](https://github.com/linuxserver/Heimdall).

---

## 🧪 Quick Comparison

| Feature                     | Homepage             | Homarr               | Heimdall            |
|----------------------------|----------------------|----------------------|---------------------|
| Config Style               | YAML (file-based)    | UI + file-based      | UI-only             |
| UI Config Editor           | ❌                    | ✔️                    | ✔️                   |
| Widgets                    | ✔️ (many integrations)| ✔️ (limited)          | ❌ (only links/cards)|
| Docker/K8s Info            | ✔️                    | ❌                    | ❌                   |
| Authentication             | 🔒 via proxy          | ✔️ (basic login)      | ❌                   |
| Mobile Friendly            | ✔️                    | ✔️                    | ❌                   |
| Project Status             | Actively maintained   | Actively maintained   | Low activity        |

---

## Homepage

**Homepage** is a highly configurable dashboard for developers and self-hosters. It relies entirely on YAML files, making it ideal for GitOps workflows.

### ✅ Pros:
- Full version-controlled config (perfect for Git).
- Widgets for Docker, Kubernetes, Uptime Kuma, and more.
- Super fast and responsive UI.
- Easily extensible.

### ❌ Cons:
- No UI config — all changes go through YAML.
- Might be intimidating for non-technical users.

> **Best for:** Advanced users who prefer infrastructure-as-code, a sleek/unified design and want deep integration with homelab services.

---

## Homarr

**Homarr** offers a more beginner-friendly, point-and-click experience. It combines a polished UI with optional JSON config export.

### ✅ Pros:
- Easily customizable through web UI.
- Supports service auto-discovery in Docker.
- Clean, modern look with drag-and-drop layout.

### ❌ Cons:
- Fewer widget integrations compared to Homepage.
- Can feel limited for more complex setups.

> **Best for:** Users who want a beautiful, no-code dashboard with enough features to get started quickly.

---

## Heimdall

**Heimdall** is the OG of homelab dashboards — a simple and functional application launcher with no bells and whistles.

### ✅ Pros:
- Very easy to set up.
- Clean interface focused on service links.
- Lightweight.

### ❌ Cons:
- No real dashboard functionality — just links.
- No widgets or integrations.
- Project activity is minimal.

> **Best for:** Minimalist setups or first-time users who just need a clickable homepage.

---

## My Pick

Personally, I went with **Homepage** for its design and the its widgets. While it took a bit longer to configure, it’s now a rock-solid part of my homelab. If you're just getting started or prefer less config, **Homarr** is an excellent alternative.

---