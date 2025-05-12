---
title: "About Me" # More specific title
date: 2025-05-12T10:48:05+02:00 # Adjusted to current date and time
draft: true
url: "/about/"
author: "Benedict"
description: "DevOps Engineer with 8+ years of experience specializing in automating internal tools to optimize costs, streamline workflows, and enhance developer efficiency." # Enhanced SEO description
---

👋 **Hello there! I'm Benedict.**

As a **DevOps Engineer** with **8+ years of experience**, I specialize in **automating internal tools to optimize costs, streamline workflows, and enhance developer efficiency**. My passion lies in building robust, scalable, and efficient infrastructure that empowers development teams to deliver software faster and with higher quality.

My journey has been focused on **bridging the gap between development and operations**, fostering a culture of automation, reliability, and continuous improvement. I'm adept at leveraging **Infrastructure as Code (IaC)**, containerization, and cloud platforms to create resilient and cost-effective systems.

I believe in a collaborative approach, constant learning, and the power of automation to simplify complexity.

---

## 📍 Connect & Find Me

* 📧 **Email:** [benedict@bndct.dev](mailto:benedict@bndct.dev)
* 🔗 **LinkedIn:** [linkedin.com/in/yourprofile](https://linkedin.com/in/yourprofile) *(Remember to update this with your actual LinkedIn URL)*
* 🐙 **GitHub:** [github.com/yourusername](https://github.com/yourusername) *(Remember to update this with your actual GitHub URL)*
* 🗺️ **Location:** Klagenfurt am Wörthersee, Carinthia, Austria

---

# 🛠️ My DevOps Toolkit & Expertise

Here's a snapshot of the technologies and practices I leverage:

### ⚙️ System Administration
* Linux, Ubuntu, Bash, Ansible, Systemd/Launchd

### 🚀 CI/CD
* CircleCI, Github Actions, Jenkins, Codeship, ArgoCD, Renovate (OSS Dependabot), KEDA

### 🐳 Containerization
* Docker, Docker Compose, Portainer, Kubernetes

### ☁️ Cloud Infrastructure
* GCP (GKE, Cloud Storage, LB, IAM), Cert-Manager, Traefik, Helm

### 🌐 Networking
* OpenVPN, SNMP, Ubiquiti, Tailscale, Wireguard

### 📊 Monitoring
* Grafana, Prometheus

### 💡 Tools
* Confluence, Jira, GitHub, Sonar, Linear

---

# 🚀 Professional Journey

### **Senior DevOps Engineer** | MeisterLabs GmbH, Vienna
December 2022 - PRESENT

* **Creating and supporting:**
    * Custom Helm charts for self-hosted Github Action runner in GKE that autoscale via KEDA.
    * RBAC setup using Google Workspace Groups as source.
    * Custom CI images with dependency management.
* **Maintaining and optimizing dozens of CI/CD pipelines** to speed up development process.

---

### **DevOps Engineer** | Bitmovin Inc, Klagenfurt
February 2019 - December 2022

* **Creating and supporting:**
    * CI/CD pipelines with various different CI platforms.
    * Google Managed Kubernetes clusters.
    * Docker environments.
* **Deploying test automation clients** for Smart TVs, Roku and other platforms via Ansible.
* **Writing scripts** to automate various workflows in Bash, Python and Node.js.

---

### **Operations Engineer** | ilogs mobile GmbH, Klagenfurt
March 2018 - December 2018

---

### **Apprenticeship** | ilogs mobile GmbH, Klagenfurt
January 2016 - March 2018

---

# ✨ Featured Projects

Here are some key projects that highlight my contributions and expertise.

### **Introducing Renovate**
As a fan of open-source software, I led the migration from Dependabot to Renovate within the company. Dependabot struggled with certain dependencies, especially Helm value files. Renovate provided better automation, broader support for everything, and more control over update schedules, making dependency management significantly more efficient and reliable.

### **Improving Security & Workflow Standardization**
Implemented Workload Identity for secure GitHub authentication to our Cloud Platform. To avoid redundant code and improve maintainability, I led the effort to create a centralized repository for shared GitHub Actions containing custom actions for authenticating via Workload Identity, handling cache, and running previously mentioned Renovate workflows.

### **CI/CD Optimization & Automation**
Designed a unified system for building custom, rootless Docker images for multiple different tech stacks. Developed a build script that generates Dockerfiles based on team-specific dependency lists, using Renovate to automate image creation when dependencies change or new GitHub Actions Runner versions are released, resulting in reduced build times and improved dependency management.

### **Scalable CI Runner Infrastructure in GKE**
The setup consists of a dynamic Helm Chart making use of my own custom GitHub Actions Runner images. This, deployed as KEDA ScaledJobs for event-based auto-scaling, enabled scaling our CI Runners based on usage, resulting in less wasted resources and cost improvements.

### **Personal Projects**
My personal homelab consists of multiple mini PCs running as a cluster thanks to TalosOS. For workloads that don’t need to be high availability, I build an Unraid Server that doubles as a network storage. Beside the core services like HomeAssistant, Plex, and Tailscale, I regularly experiment with new technologies—for example, migrating from Nginx to Caddy or exploring different deployment methods for my portfolio site.

---