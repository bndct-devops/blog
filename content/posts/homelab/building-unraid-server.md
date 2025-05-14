---
date: '2025-05-12T19:41:30+02:00'
draft: false
title: 'Part 1: Building my Unraid Server'
description: "A detailed look into my Unraid server build, covering the hardware components chosen, the steps for initial setup, and the key plugins and Docker applications that power my homelab storage and services."
tags: ["homelab", "unraid", "server build", "hardware", "nas", "storage", "docker", "plugins", "home media"]
categories: ["Homelab", "Hardware", "Storage"]
author: ["benedict"]
showToc: true
TocOpen: false
cover:
  image: "https://blog.bndct.dev/images/cover_unraid.png"

---

## Introduction: My Allrounder Server

As a DevOps Engineer and homelab tinkerer, reliable and flexible storage and a central platform to run services on is essential. That's where a home server comes in! 

Initially I just wanted something to run Home Assistant on. But after the streaming landscape got more anti-consumer with every new platform, introduction of ads in lower price tiers, the nth price increase and the overall low bitrate of content, I had to look for something that could host and play media as well.

---
## Hard choices: What to choose?

### First choice (the ugly)

My first (uninformed) instinct was to just get a small formfactor PC with a bunch of portable SSD attached. I ordered a Beelink EQ12 from Amazon, a portable Samsung SSD and set everything up. Not long after I ran into some limitations:

* **Reliability:** It worked great for Home Assistant, but I quickly noticed that having a bunch of SSDs connected via USB and playing media from them was less then great. Both the PC and the SSDs quickly ran hot, the disks randomly disconneted and the upgrade potential was small.
* **Performance:** Even tho Intel Quick Sync on the N100 chips is great, I've encountered playback issues when running a single 4k stream with higher then normal bitrate. The problem increased once subtitles came into play or I dared to have more then one stream running at a time.
---

### Second choice (the bad)

After my uninformed first try I started looking into the world of Homelabs and what more experienced people usually do. I still didn't want to invest a lot of time, so I looked into prebuilt NAS systems. I quickly aborted that approach after seeing into the top contender in that space - little upgrade potential, closed source OS and a big entry fee.

---
### Final choice (the good)

I had to bite the bullet and admit to myself that what I wanted (not needed, that's not we are here for) doesn't come of the rack. And that I'll have to invest more time into researching what I actually want to do at the moment, in the future and what's personally important to me.

* **Needs:**
    * A media server that's able to stream at least 2-3 concurrent, high bitrate media files
    * Multiple docker container for a variety of self hosted applications (*arr stack, tandoor, etc)
    * The possibility to upgrade it endlessly in the future
    * Low power consumption
    * Even lower noise production
    * Something that easily blends into my living room

After looking at my needs, before looking into the hardware aspect of it, I had to choose what OS I want to use. I quickly abandoned the idea of running on a plain Linux Server or a Kubernetes Cluster. I wanted something specifically for NAS usage without running into the problems I deal with at work. What was left over were TrueNAS, Unraid and OpenMediaVault. From all the things I saw online I didn't think much about and went with Unraid.

---
## Hardware Decisions: Balancing Power, Efficiency, and Expandability

When it came to selecting components for my Unraid server, I focused on a balance of several key factors: power efficiency (for 24/7 operation), expandability for future storage needs, and sufficient processing power for Docker containers and occasional VMs. Here's a breakdown of the hardware I chose and why:

### Case
* **Model:** Jonsbo N4
* **Why I Chose It:** Unconventional I know, but the first thing I chose was the case. I want the server to be in the living room - so a small form factor case that also looks great was pretty high in my priority list. The review especially for regarding airflow were not great, but I was confident that some well placed fans would take care of that.  
---
### CPU
* **Model:** Intel Core i5-13500 CPU
* **Why I Chose It:** This 13th Gen Intel Core i5 strikes an excellent balance between power efficiency and performance. It's more than capable of handling multiple Docker containers, Plex transcodes, and general server tasks without excessive power consumption. The integrated graphics are also a big plus if needed.
---
### Motherboard
* **Model:** Q670 NAS 8-Bay Motherboard
* **Why I Chose It:** Not many choices for a Micro-ATX case. Its native 8-bay support eliminated the need for an additional HBA card, simplifying the build and reducing potential points of failure.
---
### RAM
* **Capacity:** 32GB
* **Type/Speed:** Crucial DDR5 RAM Kit
* **Why I Chose It:** Might be a bit overkill, but I was not sure how many services I actually wanted to run. Memory is not that expensive, worst case some GBs are staying unused.
---
### Power Supply Unit (PSU)
* **Model:** be quiet! SFX Power 3 450W PSU
* **Why I Chose It:** The SFX form factor was necessary for the Jonsbo N4 case. I opted for "be quiet!" due to their reputation for silent and efficient power supplies. 450W provides more than enough power for all components, including multiple spinning hard drives, ensuring stability and leaving room for potential future upgrades.
---
### Storage Drives
* **Cache Drive:**
    * **Model/Capacity:** 2x WD_BLACK SN770 1TB SSD (NVMe)
    * **Why:** This NVMe SSD serves as my high-speed cache drive. The WD Black SN770 offers excellent read/write speeds, which is vital for quickly ingesting data into the array and providing fast access for my Docker applications.
* **Array Drive(s):**
    * **Models/Capacities:** 2x WD Red Plus 4TB HDD
    * **Why:** WD Red Plus drives are specifically designed for NAS environments, offering reliability and durability for 24/7 operation.
---
### Cooling Solutions
* **CPU Cooler:** Noctua NH-L9i Chromax Black Cooler
    * **Why I Chose It:** This low-profile cooler was a perfect fit for the compact Jonsbo N4 case and my efficient i5-13500 CPU. As an Austrian I have to support local companies, especially if they are as famous as Noctua.
* **Case Fan:** Noctua NF-S12A PWM chromax.black.swap Fan
    * **Why I Chose It:** Supplementing the case's cooling, this Noctua fan provides additional airflow.
---

## Initial Unraid Setup: Getting Started

Once the hardware was assembled, the next step was to get Unraid up and running. The process is surprisingly straightforward, even for those new to server building.

1.  **Preparing the Unraid USB Boot Drive:**
    * Download the Unraid USB Creator tool from the official website. Select the desired Unraid version and flash it to a dedicated USB stick. It's recommended to use a good quality USB 3.0 drive for longevity and boot speed.

2.  **BIOS Configuration:**
    * Accessed the motherboard's BIOS settings. Key configurations included enabling VT-d (Intel Virtualization Technology for Directed I/O) for potential VM pass-through, ensuring the boot order was set to prioritize the USB drive.

3.  **Initial WebUI Setup:**
    * Upon first boot, I accessed the Unraid WebUI via its IP address (or `tower.local` if DNS resolves). Once up and running I'd recommend to give Unraid a static IP address. 

4.  **Array Configuration:**
    * This was the core setup. My drives were assigned as the first data drive to the array. Finally, the m.2 drives were assigned as the cache drive. After reviewing the assignments, I started the array and ran a S.M.A.R.T. test to make sure all my drives are working as expected.

5.  **Enabling Docker & Virtual Machines:**
    * Within the Unraid WebUI, I navigated to `Settings -> Docker` and enabled the Docker service, configuring the `appdata` share location on my cache drive for optimal performance. Similarly, I enabled the Virtual Machines manager under `Settings -> VM Manager`.

---

## Essential Plugins in Unraid

Unraid's true power lies in its vibrant community applications ecosystem. Once the core system was stable, I started installing the plugins and Docker containers that form the backbone of my homelab services.

* **Community Applications (CA):**
    * The absolute must-have plugin. CA provides a vast app store for Docker containers and plugins, simplifying installation, updates, and management.
* **Unassigned Devices:**
    * Allows mounting and managing non-array drives (e.g., USB drives, secondary SSDs for VMs) directly within Unraid, perfect for flexible storage assignments.
* **Fix Common Problems (FCP):**
    * An essential diagnostic tool that continuously monitors your Unraid server, identifies common issues or misconfigurations, and often provides solutions or guidance.

---

## Conclusion:

I am really glad I went with the DYI solution and especially Unraid itself - my server has truly become the central pillar of my homelab, offering a powerful and flexible platform for everything from secure data storage to hosting a wide array of dockerized services.

I'm continually optimizing and expanding its capabilities, always looking for new ways to leverage its power - being the main reason I have started this blog.

---
