---
date: '2025-05-11T20:26:55+02:00'
title: "Homelab Access Architecture: Tailscale, Caddy, and Custom Domains"
draft: true
description: "Details the architectural design for secure remote access to homelab services and their exposure via custom domains using Tailscale, Caddy, and Cloudflare."
tags: ["homelab", "networking", "tailscale", "caddy", "cloudflare", "security", "reverse proxy", "VPN"]
categories: ["Homelab", "Networking"]
---

## System Overview
The primary objective of this setup is to provide secure and convenient access to internal homelab services from any location without direct exposure to the public internet. This is achieved by leveraging a trusted mesh VPN (Tailscale), a flexible reverse proxy (Caddy), and robust DNS management (Cloudflare).

## Core Components

### Tailscale: The Secure Network Fabric
Tailscale establishes a private, encrypted mesh network connecting all authenticated devices and homelab servers.
* **Access Control**: Remote access to any homelab service is exclusively facilitated through a client device connected to the Tailscale network. Direct public internet exposure of services is actively avoided.
* **Internal Routing**: All inter-service communication within the homelab, including Caddy's forwarding of requests to backend services, occurs over Tailscale's encrypted tunnels using their respective Tailscale IP addresses.

### Caddy: Reverse Proxy and SSL Automation
Caddy functions as the central reverse proxy within the Tailscale network.
* **Deployment**: Caddy is deployed on a designated server within the Tailscale network.
* **Custom Domain Handling**: It is configured to manage custom domain names (e.g., `jellyfin.bndct.dev`, `unraid.bndct.dev`), acting as the primary entry point for these domains.
* **Request Routing**: Upon receiving a request for a custom domain, Caddy intelligently proxies the request to the corresponding internal homelab service. This routing is performed using the Tailscale IP address of the target service, ensuring traffic remains within the secure Tailscale mesh.
* **Automated HTTPS**: Caddy automatically provisions and renews SSL certificates (e.g., via Let's Encrypt) for all configured custom domains. This ensures all client-to-Caddy connections are secured with HTTPS.

### Cloudflare DNS: Domain Management and SSL Challenge Facilitator
Cloudflare serves as the authoritative DNS provider for the custom domain (`bndct.dev`).
* **DNS Resolution**: Cloudflare manages the public DNS records for the custom domain. These records are configured to resolve to the Tailscale IP address of the Caddy server. This ensures that while DNS resolution is public, the resolved IP is only routable from within the Tailscale network.
* **DNS-01 Challenge Integration**: Caddy leverages Cloudflare's API to perform the DNS-01 challenge required for SSL certificate validation. This automated process involves Caddy temporarily creating and verifying specific DNS TXT records via Cloudflare's API.

## Operational Flow
The following steps outline the process for accessing a homelab service:

1.  **Client Connection**: The user's client device establishes a connection to the Tailscale network.
2.  **DNS Query**: The user initiates access by navigating to a custom domain (e.g., `plex.bndct.dev`) in their browser. The DNS query for this domain is resolved by Cloudflare, which returns the Tailscale IP address of the Caddy server.
3.  **Secure Tunnel to Caddy**: The client, being connected to Tailscale, establishes a secure, encrypted connection (HTTPS) to Caddy's Tailscale IP address.
4.  **Caddy Reverse Proxy**: Caddy receives the request. Based on the requested custom domain, it proxies the request to the specific homelab service using that service's dedicated Tailscale IP address.
5.  **Service Response**: The homelab service processes the request and returns data to Caddy, which then transmits it back to the client device over the secure Tailscale tunnel, maintaining HTTPS encryption.

## Architectural Diagram
The following diagram illustrates the described system architecture:

![Diagram](images/homelab.png)

### Configuration Advantages
This architecture provides several significant advantages:
- **Enhanced Security**: Services are not directly exposed to the public internet, minimizing the attack surface. All data transmission is secured by Tailscale's encryption and Caddy's HTTPS.
- **Streamlined Access**: Access via memorable custom domain names eliminates the need for recalling specific IP addresses or port numbers.
- **Automated SSL Management**: Caddy's automatic certificate provisioning and renewal simplify the management of HTTPS for all services.
- **Simplified Network Management**: Complex router configurations such as port forwarding are eliminated.
- **Scalability**: Integration of new services is straightforward, involving their addition to the Tailscale network and corresponding Caddy configuration.

