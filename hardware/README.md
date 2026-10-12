# 🖥️ Hardware

> The physical machines in my homelab and what each one is used for.

This is the inventory for the [Homelab](../README.md). The other sections describe what runs on these machines: [network](../network/README.md) and [kubernetes](../kubernetes).

---

## 📋 Inventory

| Host | Role | Model | CPU | RAM | Storage |
|---|---|---|---|---|---|
| `jayden1` | k3s control plane | Dell OptiPlex 7040 | Intel Core i5-6500T, 4 cores, 2.5 GHz | 8 GB | 256 GB SATA SSD |
| `jayden2` | k3s worker | Dell OptiPlex 7040 | Intel Core i5-6500T, 4 cores, 2.5 GHz | 8 GB | 256 GB SATA SSD |
| `jayden3` | k3s worker | Dell OptiPlex 7040 | Intel Core i5-6500T, 4 cores, 2.5 GHz | 8 GB | 256 GB SATA SSD |
| `megatron` | Proxmox VE hypervisor | HP Pavilion desktop | AMD Ryzen 7, 5000 series | 16 GB | 1 TB |
| OPNsense | Firewall, router, DHCP, DNS | Dell OptiPlex 3020 | Intel Core i3 | 8 GB | 256 GB |

---

## ☸️ Kubernetes Nodes

Three identical small-form-factor desktops run the k3s cluster on bare metal.

| | |
|---|---|
| **Model** | Dell OptiPlex 7040 |
| **CPU** | Intel Core i5-6500T (6th generation, 4 cores, 4 threads, 2.5 GHz, low-power "T" variant) |
| **Memory** | 8 GB each |
| **Disk** | One 256 GB SATA SSD each |
| **BIOS** | Dell 1.24.0 |
| **OS** | Ubuntu 26.04 LTS, kernel 7.0 |

**Cluster totals:** 12 cores, 24 GB of memory, 768 GB of SSD.

### SSDs

| Node | Drive |
|---|---|
| `jayden1` | Samsung MZ7LH256 |
| `jayden2` | Samsung MZ7LN256 |
| `jayden3` | Micron 1100 |

### Disk layout

Each node has the same layout, created by the Ubuntu installer.

| Partition | Size | Use |
|---|---|---|
| `sda1` | 1 GB | EFI system partition |
| `sda2` | 2 GB | `/boot` |
| `sda3` | 252 GB | LVM physical volume |

The root filesystem is a 100 GiB logical volume inside that LVM partition. The installer leaves the rest unallocated, so each node has about 145 GB of SSD space that is not assigned to any volume yet.

---

## 🖧 Hypervisor

`megatron` runs Proxmox VE and hosts the lab's virtual machines and containers.

| | |
|---|---|
| **Model** | HP Pavilion desktop |
| **CPU** | AMD Ryzen 7, 5000 series |
| **Memory** | 16 GB |
| **Storage** | 1 TB |
| **GPU** | NVIDIA GeForce GTX 1050 Ti, used by the Jellyfin server for hardware video encoding (NVENC) |

It has the most memory and disk of any machine in the lab, which is why the workloads that need both run here instead of on the cluster nodes.

---

## 🔥 Firewall

OPNsense runs on a dedicated machine, so the firewall stays up when the hypervisor or the cluster is being worked on.

| | |
|---|---|
| **Model** | Dell OptiPlex 3020 |
| **CPU** | Intel Core i3 |
| **Memory** | 8 GB |
| **Storage** | 256 GB |
| **Role** | Edge firewall and router for the lab subnet, plus DHCP, DNS and the Tailscale subnet router |

---

## 📊 Headroom

Typical usage on the Kubernetes nodes with the monitoring stack, Argo CD and Uptime Kuma running:

| Node | CPU in use | Memory in use |
|---|---|---|
| `jayden1` | 6% | 30% (2.2 GB) |
| `jayden2` | 2% | 14% (1.0 GB) |
| `jayden3` | 3% | 25% (1.9 GB) |

CPU is barely touched. Memory is the limit that will be reached first, at 8 GB per node.

---

## 🧭 What the Hardware Allows

- **One disk per node.** The cluster currently uses the `local-path` provisioner, which stores each volume in a folder on a single node's SSD.
- **Rook Ceph is not a good fit as built.** Ceph needs a dedicated raw disk on every node and several gigabytes of memory per node. These machines have one disk, shared with the operating system, and 8 GB of memory.
- **Replicated storage is still possible.** The unallocated LVM space on each node could back a lighter system such as Longhorn, which works from a directory or volume on the existing disk.
- **Upgrade paths.** More memory per node is the upgrade that would unlock the most. A second drive in each node would make Ceph practical.
