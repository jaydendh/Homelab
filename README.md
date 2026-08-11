# Homelab

# 🏠 Home Lab Infrastructure

> An on-premises, self-hosted infrastructure platform built on bare metal — segmented behind an OPNsense firewall, running a Proxmox virtualization environment and a k3s Kubernetes cluster, with full observability and zero-trust remote access.

This repository is the **hub** for my home lab. It documents the underlying platform — hardware, networking, virtualization, and orchestration — that everything else runs on. Individual workloads and projects live in their own repositories and link back here (see [Projects](#-projects-running-on-this-lab)).

---

## 📌 Overview

This lab is a hands-on environment for practicing **infrastructure, networking, virtualization, container orchestration, observability, and DevSecOps** — the same disciplines I work in professionally. It is designed to mirror production patterns at a small scale: network segmentation, least-privilege access, monitoring, and infrastructure-as-code.

**Key capabilities:**
- 🔥 **Network segmentation** — the lab is isolated behind an OPNsense edge firewall with its own subnet, NAT, DHCP, and DNS
- 🖥️ **Virtualization** — a Proxmox VE hypervisor hosting VMs and LXC containers
- ☸️ **Container orchestration** — an on-prem k3s Kubernetes cluster with dynamic NFS-backed storage
- 📊 **Observability** — Prometheus + Grafana for metrics, dashboards, and cluster health
- 🔐 **Zero-trust remote access** — Tailscale (WireGuard mesh) for secure access from anywhere, no ports exposed to the internet
- 💾 **Network storage** — TrueNAS serving ZFS-backed NFS shares to the cluster and media services

---

## 🗺️ Architecture

![Home Lab Architecture](architecture.svg)

---

## 🧰 Tech Stack

| Layer | Technology |
|---|---|
| **Firewall / Routing** | OPNsense (BSD-based) |
| **Virtualization** | Proxmox VE |
| **Network Storage** | TrueNAS (ZFS) over NFS |
| **Orchestration** | k3s (lightweight Kubernetes) |
| **Package Management** | Helm |
| **Persistent Storage** | nfs-subdir-external-provisioner (dynamic NFS PVCs) |
| **Monitoring** | Prometheus, Grafana |
| **Remote Access** | Tailscale (WireGuard mesh VPN) |
| **Media Server** | Jellyfin |

---

## 🌐 Network Design

The lab sits **behind** the primary/family network on a dedicated segment, enforcing isolation between lab infrastructure and the rest of the home.

- **WAN:** OPNsense uplinks to the upstream network (double-NAT by design)
- **LAN:** `192.168.10.0/24` — the isolated lab subnet
- **Gateway / DNS:** OPNsense (`192.168.10.1`)
- **Addressing:** static assignments / DHCP reservations for infrastructure hosts
- **Remote access:** Tailscale advertises the lab subnet, allowing secure access to any host from anywhere without exposing ports to the internet

**Firewall approach:** default-deny inbound from WAN, with explicit rules governing what the lab can reach — practicing least-privilege segmentation.

---

## 💾 Storage

- **TrueNAS** manages a ZFS pool and exports it over **NFS**.
- **Jellyfin** and the k3s cluster consume media/storage from the same NFS export, ensuring consistent paths (important for hardlink-based media workflows).
- **k3s** uses the `nfs-subdir-external-provisioner` to dynamically provision `ReadWriteMany` PersistentVolumes backed by NFS.

---

## 📊 Observability

- **Prometheus** scrapes cluster and node metrics.
- **Grafana** provides dashboards for cluster health, resource utilization, and workload monitoring.

---

## 🔐 Remote Access

- **Tailscale** runs on OPNsense as a subnet router, advertising `192.168.10.0/24`.
- Provides encrypted (WireGuard) access to the entire lab from any device on my tailnet — **no inbound ports opened** on the network.
- Route approval and interface firewall rules scope what the tailnet can reach.

---

## 🔗 Projects Running on This Lab

Each project below is deployed on this infrastructure and lives in its own repository:

| Project | Description | Repo |
|---|---|---|
| **Media Automation Stack** | Containerized media pipeline (Sonarr, Radarr, Prowlarr, qBittorrent, Jellyseerr) on k3s with automated malware scanning (ClamAV) | _link_ |
| **Monitoring Stack** | Prometheus + Grafana deployment, dashboards, and alert rules | _link_ |
| _more coming_ | | |

> Each spoke repo links back here for full infrastructure context.

---

## 📚 What I Learned / Demonstrated

- Bare-metal firewall deployment and network segmentation with OPNsense
- Diagnosing and resolving low-level networking issues (NIC/driver, routing, NAT, ARP)
- Migrating an entire cluster to a new subnet and recovering NFS storage mounts
- Kubernetes storage provisioning and Helm-based application deployment on k3s
- Building a monitoring/observability stack from scratch
- Implementing zero-trust remote access with WireGuard/Tailscale
- Virtualization management with Proxmox (VMs, LXC, storage passthrough)

---

## 📄 License

Personal project — documentation shared for reference and portfolio purposes.
