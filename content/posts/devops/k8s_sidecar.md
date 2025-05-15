---
date: '2025-05-15T17:56:12+02:00'
draft: false
title: 'True Sidecar Containers'
tags: ["kubernetes", "container", "k8s"]
categories: ["Kubernetes", "Container", "k8s"]
author: ["benedict"]
showToc: true
TocOpen: false
cover:
  image: "https://blog.bndct.dev/images/cover_sidecar.png"
---

## Finally a solution to a long lived problem

Sometimes working in DevOps means tackling problems in a less then ideal way. Sometimes it's because of a lack of time, sometimes it is because there is just no better solution to it. For me that problem was having a second container in a pod acting independently from the "main" container. 

Before Kubernetes introduced actual sidecar containers, the sidecar pattern was implemented using regular containers in the Pod’s containers section. The problem was that all containers (including the sidecar) shared the same lifecycle, making it hard to manage startup and shutdown independently.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: legacy-sidecar-example
spec:
  containers:
    - name: container_a
      image: busybox
      command: ["/bin/sh", "-c"]

    - name: container_b
      image: busybox
      command: ["/bin/sh", "-c"]
```

Unlike `initContainer` which start **and** terminate before `container_a` is created, you can define two container running side by side at the same time. This might be useful for running a SQL server next to your self-hosted CI runner - f.e. if you tests need to validate against a running database.

Now comes the problem: once `container_a` finishes, `container_b` keeps running. Easy fix right? You could just:

- keep pinging `container_a` till it's unreachable and terminate `container_b`
- write to a shared log file and terminate `container_b` once it stops growing

You can see how that is aesthetically unappealing at best and potentially unreliable at worst, correct?

---

# The solution is here

It seems like I wasn't the only one having such a use case and being extremely unsatisfied with the solution. With the introduction of [KEP-753](https://github.com/kubernetes/enhancements/issues/753) and **Kubernetes v1.29**, Kubernetes now supports **true sidecar containers** — giving them distinct lifecycle behavior, better suited for real-world use cases.

---

## What Changed exactly?

Now, within `initContainers`, Kubernetes defines **sidecar containers as a lifecycle mode** within `initContainers`:

- **They can run alongside app containers**.
- **They start early and continue running**, without blocking app startup.
- **They are defined as init containers** but with a special lifecycle.

---

## Requirements

To use this feature, you need:
- Kubernetes **v1.29+**
- Feature gate `SidecarContainers=true` **enabled**
- Sidecar container defined in `initContainers` with `restartPolicy: Always`

---

## YAML Example: True Sidecar Container

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: shiny-sidecar-example
spec:
  initContainers:
    - name: sidecar-container
      image: busybox
      restartPolicy: Always
  containers:
    - name: main-container
      image: busybox
```

## Breakdown
The sidecar container is defined in the `initContainers` section, but with `restartPolicy: Always`, which makes it behave like a true sidecar.

The main container starts immediately — it isn't blocked by the "init" container, since the sidecar is always-running.

## Benefits
Predictable lifecycle: Sidecars no longer exit prematurely or hold up app startup.

Cleaner shutdown logic: You can gracefully separate sidecar and app behavior.

Better support for service meshes, log forwarders, config watchers, etc.
