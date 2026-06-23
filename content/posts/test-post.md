---
title: "Linux 内核调度器浅析"
date: 2026-06-22T10:00:00+08:00
draft: false
tags: ["Linux", "内核", "调度器"]
categories: ["技术"]
summary: "简单介绍 Linux 内核 CFS 调度器的基本原理。"
---

## 概述

Linux 内核从 2.6.23 版本开始使用 **CFS（Completely Fair Scheduler）** 作为默认调度器。CFS 的核心思想是公平分配 CPU 时间。

## vruntime

CFS 使用虚拟运行时间（vruntime）来跟踪每个进程应得的 CPU 时间：

```
实际运行时间 × (NICE_0_LOAD / 进程权重) = vruntime
```

## 红黑树

就绪队列通过红黑树组织，键值为 vruntime。每次调度时，选择 vruntime 最小的节点（即红黑树最左侧的进程）来运行。

## 调度周期

```c
// 计算时间片
slice = (sched_period * task_weight) / total_weight
```

- 进程数少时，调度周期较短
- 进程数多时，每个进程分到的时间片相应减少
- 默认调度周期为 6ms（`sysctl_sched_latency`）

## 唤醒抢占

当新进程被唤醒且其 vruntime 比当前运行进程小足够多时，会触发抢占：

```c
if (wakeup_preempt_entity(se, pse) == 1) {
    resched_curr(rq);
}
```

CFS 的设计使得调度延迟保持在一个合理范围内，同时保证了公平性。
