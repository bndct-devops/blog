---
date: '2025-05-21T20:54:26+02:00'
draft: false
title: 'Effortless Homelab Monitoring with Beszel'
description: "My go-to solution for lightweight monitoring in a self-hosted environment"
tags: ["homelab", "container", "monitoring"]
categories: ["Homelab", "Container", "Monitoring"]
author: ["benedict"]
showToc: true
TocOpen: false
cover:
  image: "https://blog.bndct.dev/images/cover_beszel.png"
---

##  Why I use Beszel for monitoring my Homelab

When it comes to Homelab tinkering and DevOps projects, monitoring is a must — but I’ve don't want to spend hours setting up/maintaining tools just to know if a VM or container is online. After testing solutions like Grafana/Prometheus or Glances, Beszel has become my favorite lightweight option. It’s simple, fast to deploy, and does exactly what I need without the overhead.

I run it on Unraid as a Docker container, with agents installed both on Unraid itself and my Home Assistant VM. That’s all I need to monitor the core parts of my homelab.

##  What is Beszel?

[**Beszel**](https://github.com/henrygd/beszel) is a minimal, no-nonsense service checker. It pings your defined endpoints and returns a JSON API with simple health statuses: up, down, or timeout. You can optionally install agents to collect system metrics like CPU and RAM, but even without them, it's incredibly useful.

It doesn't require a database or complex stack — just a config file and a container, and you're good to go.

##  My Setup
It's actually stupid easy. 

1. Set up Beszel Hub running as a Docker container on Unraid
2. Set up Beszel Agent running as a Docker container on Unraid/Home Assistant
3. That's it. No config needed, pulled all my container info right away

There is the option to configure stuff manually, but I did not need to at all. Here is the generated `config.yaml` that Beszel exported for me.

```yaml
systems:
    - name: homeassistant
      host: homeassistant.bndct.dev
      port: 45876
      users:
        - benedict@bndct.dev
    - name: unraid
      host: unraid.bndct.dev
      port: 45876
      users:
        - benedict@bndct.dev
```

##  Integration with Homepage

One of my favorite features of Beszel is how easy it is to plug into [Homepage](https://gethomepage.dev/widgets/services/beszel/).
I use this every day — it’s the first thing I check to make sure everything is running smoothly.

##  Final Thoughts

Beszel might not be as fancy as something like Prometheus/Grafana, but that’s why I use it. It’s lightweight, fast to set up, and does the job. For my homelab, that’s a win.

