# 💾 TrueNAS

> Network storage for the lab: a TrueNAS virtual machine on Proxmox, managing a ZFS pool and sharing it over NFS and SMB.

This is a guest on the [Proxmox host](../README.md), part of my [Homelab](../../README.md).

---

## 📌 Overview

| | |
|---|---|
| **Runs as** | Virtual machine on `megatron` |
| **Filesystem** | ZFS |
| **Sharing protocols** | NFS and SMB |
| **Management** | Web interface over HTTPS |

---

## 🎯 What It Is For

TrueNAS gives the lab one place for bulk data that several systems need at once.

| Consumer | Protocol | Use |
|---|---|---|
| [Jellyfin](../jellyfin/README.md) | NFS | Reads the media library |
| k3s cluster | NFS | Planned: shared storage for pods that must see the same files from different nodes |
| Desktops and laptops | SMB | File access |

Serving the media to Jellyfin and to the cluster from the same NFS export will keep file paths identical on both sides, which matters for media tools that move files with hardlinks.

---

## 🧱 Why ZFS

- **Checksums on everything.** ZFS detects silent corruption and, with redundancy, repairs it.
- **Snapshots.** A point-in-time copy of a dataset costs almost nothing and can be rolled back.
- **Datasets.** Each share can have its own quota, compression and permission settings.

---

## 🧩 Why a Virtual Machine

TrueNAS is a complete operating system, so it cannot run as a container. It also wants direct control of its disks to manage ZFS properly, which a VM allows by passing storage through from the host.

---

## 🔗 Why It Matters to Kubernetes

The cluster's built-in `local-path` storage keeps each volume on a single node's disk, with the `ReadWriteOnce` access mode. Anything that needs `ReadWriteMany`, where pods on different nodes mount the same volume, needs network storage. An NFS export from TrueNAS provides that, and connecting the two is the next step.

---

## 🔭 Next Steps

- [ ] Document the pool layout, datasets and exports
- [ ] An NFS provisioner on the cluster, so PersistentVolumeClaims are created on TrueNAS automatically
- [ ] Scheduled snapshots and a replication target for backups
