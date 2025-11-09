# 🧠 Create Kubernetes Cluster Using Kubeadm

[![Vagrant](https://img.shields.io/badge/Vagrant-2.4.1-blue?logo=vagrant)](https://www.vagrantup.com)
[![VirtualBox](https://img.shields.io/badge/VirtualBox-7.0-green?logo=virtualbox)](https://www.virtualbox.org)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.34.0-3264c6?logo=kubernetes)](https://kubernetes.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> **Build a multi-node Kubernetes cluster locally** using **Vagrant + VirtualBox + Kubeadm**.  
> Ideal for **learning, testing, and development**.

---

## 📋 Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Cluster Setup](#cluster-setup)
  - [1. Initialize Control Plane](#1-initialize-control-plane)
  - [2. Join Worker Nodes](#2-join-worker-nodes)
  - [3. Install CNI (Flannel)](#3-install-cni-flannel)
- [Verification](#verification)
- [Troubleshooting](#troubleshooting)
- [Cleanup & Reset](#cleanup--reset)
- [Vagrant Commands](#vagrant-commands)
- [Pro Tips](#pro-tips)
- [Contributing](#contributing)
- [License](#license)
- [Author](#author)

---

## 🎯 Overview

This project automates the creation of a **3-node Kubernetes cluster**:
- `controlplane` → Master Node
- `node01`, `node02` → Worker Nodes

Using:
- **Vagrant** → VM provisioning
- **VirtualBox** → Hypervisor
- **Kubeadm** → Cluster bootstrapping
- **containerd** → Container runtime
- **Flannel** → Pod networking

---

## 🏗️ Architecture

```ascii
+------------------+       +------------------+       +------------------+
|  controlplane    |       |     node01       |       |     node02       |
| (Master)         |       | (Worker)         |       | (Worker)         |
| - kube-apiserver |       | - kubelet        |       | - kubelet        |
| - etcd           |       | - containerd     |       | - containerd     |
| - scheduler      |       | - flannel        |       | - flannel        |
| - controller     |       +------------------+       +------------------+
+------------------+
        ↑              ↑              ↑
        |              |              |
        +--------------+--------------+
                       |
               +------------------+
               |   Host Machine   |
               | - VirtualBox     |
               | - Vagrant        |
               +------------------+
