---
title: "虚拟化技术概述：从 Hypervisor 到容器"
date: 2026-06-21T14:30:00+08:00
draft: false
tags: ["虚拟化", "KVM", "容器", "QEMU"]
categories: ["技术"]
summary: "梳理虚拟化技术的发展脉络，从全虚拟化到容器。"
---

## 虚拟化的本质

虚拟化的核心目标是通过**资源抽象**，让单个物理机的 CPU、内存、I/O 被多个虚拟机共享。这个抽象层被称为 **VMM（Virtual Machine Monitor）** 或 **Hypervisor**。

## 分类

### Type-1 Hypervisor（裸机型）

直接运行在硬件之上，内核即为 VMM：

- **KVM** — Linux 内核模块，依赖硬件虚拟化（Intel VT-x / AMD-V）
- VMware ESXi
- Xen
- Microsoft Hyper-V

### Type-2 Hypervisor（宿主型）

运行在宿主操作系统之上：

- VirtualBox
- VMware Workstation
- QEMU（用户态模拟）

## KVM + QEMU 协作

Linux KVM 提供内核态的 CPU 和内存虚拟化，QEMU 负责设备模拟（网卡、磁盘、VGA 等）：

```
Guest VM
    ↕ (ioctl /dev/kvm)
KVM kernel module
    ↕
QEMU (设备模拟)
    ↕
Linux Host Kernel
```

## 半虚拟化（Paravirtualization）

虚拟机感知自身已被虚拟化，通过 **virtio** 驱动与宿主机协作，避免昂贵的 MMIO 模拟：

```c
// virtio 框架简化示意
struct virtqueue {
    struct vring vring;       // 共享内存环形队列
    void (*notify)(struct virtqueue *vq); // kick host
};
```

## 容器 vs 虚拟机

| | 虚拟机 | 容器 |
|---|---|---|
| 隔离级别 | Hypervisor 硬件级 | 内核 namespace + cgroup |
| 启动速度 | 秒级 | 毫秒级 |
| 密度 | 数十 | 数百 |
| 内核 | 独立内核 | 共享宿主机内核 |
| 安全性 | 强隔离 | 内核攻击面共享 |

## 嵌套虚拟化

在 VM 内再次运行 VM，KVM 通过 `vmx` 模块支持：

```bash
# 宿主机开启嵌套虚拟化
modprobe kvm_intel nested=1
```

虚拟化技术已从单一的全虚拟化演进为包含硬件辅助、半虚拟化、容器等多种方案的生态系统。
