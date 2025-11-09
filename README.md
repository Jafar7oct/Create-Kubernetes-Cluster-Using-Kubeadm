# 🧠 Create-Kubernetes-Cluster-Using-Kubeadm

Hey there, cloud wizard! 🧙‍♂️  
This guide helps you **create your own Kubernetes cluster** locally using **Vagrant + VirtualBox + Kubeadm**.  
Perfect for DevOps learners, cloud engineers, and anyone who loves containers more than coffee ☕🐳

---

## ⚙️ Prerequisites

Before we start the magic 🪄, make sure you have these tools installed:

| Tool | Description | Download Link |
|------|--------------|----------------|
| 🧱 **VirtualBox** | Creates the virtual machines (VMs) that will form your cluster. | [Download VirtualBox](https://www.virtualbox.org/wiki/Downloads) |
| 📦 **Vagrant** | Automates the creation and configuration of those VMs. | [Download Vagrant](https://developer.hashicorp.com/vagrant/downloads) |

---

## 🚀 Getting Started

### 1️⃣ Clone the Repository
```bash
git clone https://github.com/Jafar7oct/Create-Kubernetes-Cluster-Using-Kubeadm.git


👉 Why?
We need to get all the configuration files (like the Vagrantfile) that describe your cluster setup.


## 2️⃣ Enter the Project Folder

```bash
cd Create-Kubernetes-Cluster-Using-Kubeadm

👉 Why?
We need to be inside the directory that contains the Vagrantfile — that’s where the magic happens 🪄.


## 3️⃣ Launch Your Cluster VMs!

```bash
vagrant up
👉 What it does:
Starts creating and configuring all VMs defined in the Vagrantfile (Control Plane + Worker Nodes).
Go grab a snack 🍕 — it might take a few minutes ⏳.


## 4️⃣ Check VM Status

```bash
vagrant status
👉 Why?
To make sure all your VMs are running and healthy 💪.

🧹 Other Useful Vagrant Commands


#### Command,Description,Emoji
vagrant halt     ,Stops all running VMs (like pausing Netflix 🎬),🛑
vagrant destroy  ,Removes all VMs completely (clean slate 💣),🔥
vagrant reload   ,Reboots VMs and re-applies changes to the Vagrantfile,♻️

vagrant ssh <vm-name>,  Connects to a specific VM (like remote login),🧑‍💻


