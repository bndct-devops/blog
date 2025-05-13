---
date: '2025-05-12T19:41:30+02:00'
draft: true
title: 'Building my Unraid Server'
description: "A detailed look into my Unraid server build, covering the hardware components chosen, the steps for initial setup, and the key plugins and Docker applications that power my homelab storage and services."
tags: ["homelab", "unraid", "server build", "hardware", "nas", "storage", "docker", "plugins", "home media"]
categories: ["Homelab", "Hardware", "Storage"]
author: ["benedict"]
showToc: true
TocOpen: false
---

## Introduction: The Heart of My Homelab - My Unraid Server

As a DevOps Engineer and an avid homelab tinkerer, reliable and flexible storage is crucial for my projects, media, and backups. That's where Unraid comes in! It offers a unique blend of NAS capabilities, Docker container hosting, and virtual machine support, all while being incredibly versatile with diverse hardware configurations.

In this post, I'll walk you through the journey of building my Unraid server, from the carefully selected components to the initial setup process, and finally, the essential plugins and Docker applications that make it the powerhouse of my homelab. Whether you're looking to build your first server or optimize an existing one, I hope my experience provides some valuable insights.

---

## My Unraid Server Hardware Choices: Balancing Power, Efficiency, and Expandability

When it came to selecting components for my Unraid server, I focused on a balance of several key factors: power efficiency (for 24/7 operation), expandability for future storage needs, and sufficient processing power for Docker containers and occasional VMs. Here's a breakdown of the hardware I chose and why:

### 1. CPU:
* **Model:** Intel Core i5-13500 CPU
* **Why I Chose It:** This 13th Gen Intel Core i5 strikes an excellent balance between power efficiency and performance. With its efficient cores and strong single-thread performance, it's more than capable of handling multiple Docker containers, Plex transcodes, and general server tasks without excessive power consumption. The integrated graphics are also a plus for initial setup or basic display needs.

### 2. Motherboard: The Foundation
* **Model:** Q670 NAS 8-Bay Motherboard
* **Key Features:** This motherboard was a standout choice due to its integrated support for 8 SATA bays, which is ideal for a dedicated NAS. It supports the latest Intel CPUs and offers a robust platform for a 24/7 server, ensuring reliability and future expansion possibilities.
* **Why I Chose It:** Its native 8-bay support eliminated the need for an additional HBA card, simplifying the build and reducing potential points of failure. The Q670 chipset provides a stable and feature-rich foundation for Unraid.

### 3. RAM: Memory for Smooth Operations
* **Capacity:** 32GB
* **Type/Speed:** Crucial DDR5 RAM Kit
* **Why I Chose It:** 32GB of DDR5 RAM is a sweet spot for an Unraid server. It provides ample headroom for running numerous Docker containers concurrently, hosting several virtual machines, and ensures the system remains responsive even under heavy load. DDR5 also offers a performance uplift for the 13th-gen CPU, contributing to overall system snappiness.

### 4. Case: Housing the Components
* **Model:** Jonsbo N4 Case
* **Why I Chose It:** The Jonsbo N4 is a popular choice for compact NAS builds, and it immediately caught my eye. Its small footprint is perfect for my homelab setup, yet it manages to cleverly pack in excellent drive bay capacity (8 bays!) and maintain good airflow, which is crucial for drive longevity and quiet operation. Plus, its aesthetic is a nice bonus.

### 5. Power Supply Unit (PSU): The Lifeline
* **Model:** be quiet! SFX Power 3 450W PSU
* **Rating:** `[You can specify efficiency rating here, e.g., 80 Plus Bronze/Gold/Silver]`
* **Why I Chose It:** The SFX form factor was necessary for the Jonsbo N4 case. I opted for "be quiet!" due to their reputation for silent and efficient power supplies. 450W provides more than enough power for all components, including multiple spinning hard drives, ensuring stability and leaving room for potential future upgrades.

### 6. Storage Drives: Parity, Cache, and Array
* **Cache Drive:**
    * **Model/Capacity:** WD_BLACK SN770 1TB SSD (NVMe)
    * **Why:** This NVMe SSD serves as my high-speed cache drive. The WD Black SN770 offers excellent read/write speeds, which is vital for quickly ingesting data into the array and providing fast access for my Docker applications. 1TB provides ample space for my appdata and temporary downloads.
* **Array Drive(s):**
    * **Models/Capacities:** WD Red Plus 4TB HDD (Currently one, with plans for expansion)
    * **Why:** WD Red Plus drives are specifically designed for NAS environments, offering reliability and durability for 24/7 operation. The 4TB capacity provides a solid starting point for my data storage needs, and with 8 bays available, I have plenty of room to expand the array by adding more of these reliable drives as my storage requirements grow.
* **Parity Drive:**
    * **Model/Capacity:** `[If you have a dedicated parity drive, specify it here. Ideally, it would be a 4TB or larger HDD, matching or exceeding your largest data drive.]`
    * **Why:** `[Explain its role, e.g., "This drive provides single-drive parity protection, ensuring that if one data drive fails, I can rebuild my array without data loss, crucial for data integrity in a homelab environment."]`

### 7. Cooling Solutions: Keeping Temps in Check
* **CPU Cooler:** Noctua NH-L9i Chromax Black Cooler
    * **Why I Chose It:** This low-profile cooler was a perfect fit for the compact Jonsbo N4 case and my efficient i5-13500 CPU. Noctua is renowned for its quiet operation and excellent cooling performance, which helps maintain optimal CPU temperatures even under load, ensuring the server runs quietly.
* **Case Fan:** Noctua NF-S12A PWM chromax.black.swap Fan
    * **Why I Chose It:** Supplementing the case's cooling, this Noctua fan provides additional airflow. Its PWM capability allows the server to adjust fan speed based on temperature, keeping noise levels to a minimum when idle and providing effective cooling when needed. The chromax.black.swap aesthetic also matches the overall build.

---

## Initial Unraid Setup: Getting Started

Once the hardware was assembled, the next step was to get Unraid up and running. The process is surprisingly straightforward, even for those new to server building.

1.  **Preparing the Unraid USB Boot Drive:**
    * Download the Unraid USB Creator tool from the official website. Select the desired Unraid version and flash it to a dedicated USB stick. It's recommended to use a good quality USB 3.0 drive for longevity and boot speed.

2.  **BIOS Configuration:**
    * Accessed the motherboard's BIOS settings. Key configurations included enabling VT-d (Intel Virtualization Technology for Directed I/O) for potential VM pass-through, ensuring the boot order was set to prioritize the USB drive, and confirming SATA controllers were set to AHCI mode for optimal drive performance.

3.  **Initial WebUI Setup:**
    * Upon first boot, I accessed the Unraid WebUI via its IP address (or `tower.local` if DNS resolves). This involved setting up basic network configurations (opting for a static IP or DHCP reservation on my router for easy access) and registering the Unraid license key.

4.  **Array Configuration:**
    * This was the core setup. I assigned my `[Your Parity Drive Model/Capacity]` as the parity drive. My `[Your Array Drive Model/Capacity]` was assigned as the first data drive to the array. Finally, the WD_BLACK SN770 1TB NVMe SSD was assigned as the cache drive. After reviewing the assignments, I started the array and let the initial parity sync complete, which is crucial for data redundancy.

5.  **Enabling Docker & Virtual Machines:**
    * Within the Unraid WebUI, I navigated to `Settings -> Docker` and enabled the Docker service, configuring the `appdata` share location on my cache drive for optimal performance. Similarly, I enabled the Virtual Machines manager under `Settings -> VM Manager`.

---

## Essential Plugins & Docker Apps for My Homelab

Unraid's true power lies in its vibrant community applications ecosystem. Once the core system was stable, I started installing the plugins and Docker containers that form the backbone of my homelab services.

### 1. Core Plugins for Unraid Management
* **Community Applications (CA):**
    * The absolute must-have plugin. CA provides a vast app store for Docker containers and plugins, simplifying installation, updates, and management.
* **Unassigned Devices:**
    * Allows mounting and managing non-array drives (e.g., USB drives, secondary SSDs for VMs) directly within Unraid, perfect for flexible storage assignments.
* **Fix Common Problems (FCP):**
    * An essential diagnostic tool that continuously monitors your Unraid server, identifies common issues or misconfigurations, and often provides solutions or guidance.
* `[Add any other core plugins you use, e.g., User Scripts for automation, NerdTools for command-line utilities, etc., and explain their purpose.]`

### 2. Key Docker Containers Powering My Services
* **Media Server:** `[e.g., Plex Media Server / Jellyfin]`
    * `[Briefly describe its function - "My central media server for movies, TV shows, and music, accessible across all my devices."]`
    * `[Optional: Link to a dedicated post on your media server setup if you plan one.]`
* **Download Automation:** `[e.g., Sonarr, Radarr, qBittorrent (or Transmission/NZBGet)]`
    * `[Briefly describe their function - "Automate the downloading and organization of my media library, seamlessly integrating with my media server."]`
* **Home Automation:** `[e.g., HomeAssistant]`
    * `[Briefly describe its function - "The hub for my smart home devices and automations, bringing convenience and control."]`
    * `[Optional: Link to a dedicated post on your HomeAssistant setup.]`
* **Network Services:** `[e.g., AdGuard Home / Pi-hole, Tailscale Client (if not native)]`
    * `[Briefly describe their function - "Provides network-wide ad-blocking and DNS resolution, or secures network access within my homelab."]`
* **File Management/Sync:** `[e.g., Krusader (or FileBrowser/Nextcloud)]`
    * `[Briefly describe their function - "Tools for managing files directly on the server or providing cloud sync capabilities."]`
* **Monitoring:** `[e.g., Grafana, Prometheus (as containers)]`
    * `[Briefly describe their function - "Visualizing server performance and application metrics for a comprehensive overview of my homelab health."]`
* `[Add any other significant Docker containers you run, explaining their purpose.]`

---

## Conclusion: The Foundation of My Homelab

My Unraid server has truly become the central pillar of my homelab, offering a powerful and flexible platform for everything from secure data storage to hosting a wide array of Dockerized services. Its modular design and robust community support make it an ideal choice for anyone looking to build a versatile home server.

I'm continually optimizing and expanding its capabilities, always looking for new ways to leverage its power. What are your thoughts on Unraid, or what essential services do you run on your home server? Share your experiences in the comments below!

---
