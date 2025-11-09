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


## 5️⃣ SSH into the Machines

```bash
vagrant ssh controlplane
vagrant ssh node01
vagrant ssh node02

👉 Why?
We’ll configure Kubernetes manually using kubeadm on these machines.



## 🏗️ Install and Configure Prerequisites (Inside Each VM)

### 🚫 Disable Swap

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^$$ .* $$$/#\1/g' /etc/fstab



### 🌐 Enable IPv4 Packet Forwarding

```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
EOF
sudo sysctl --system
sysctl net.ipv4.ip_forward

👉 Why?
This lets your cluster nodes communicate and forward packets properly.
Without it, pods can’t talk — and that’s sad 😢.


### 📦 Install Kubernetes Tools

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl



👉 Why?
These are the 3 amigos of Kubernetes:

kubelet → Runs on every node and talks to the control plane 🗣️
kubeadm → Bootstraps your cluster 🎯
kubectl → The boss command-line tool 🕹️


### 🐳 Install and Configure containerd (Container Runtime)

```bash
sudo apt install -y containerd
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml

👉 Why?
Kubernetes needs a container runtime to run containers.
containerd is fast, lightweight, and officially supported by Kubernetes 🏎️.


### 🧩 Edit `config.toml` for systemd cgroup driver

Edit `/etc/containerd/config.toml` and make sure this section looks like:

```toml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
  SystemdCgroup = true

👉 Why?
This ensures containerd uses the systemd cgroup driver — required for proper integration with kubelet and Kubernetes resource management. Mismatched drivers = chaos! 🚫

### 🔁 Restart and Enable containerd

```bash
sudo systemctl restart containerd
sudo systemctl enable containerd

👉 Why?
Restarts the containerd service with the new config and ensures it starts automatically on boot — so your cluster stays up and running! 🔄


## 🧠 Initialize the Control Plane

```bash
sudo kubeadm init --apiserver-advertise-address <CONTROLPLANE_IP> \
  --pod-network-cidr=10.244.0.0/16 --upload-certs

👉 Flags explained:
Flag,Purpose
--apiserver-advertise-address <CONTROLPLANE_IP>      ,IP of your control plane (master node) — tells workers where to reach the API server 🌍
--pod-network-cidr=10.244.0.0/16                     ,Must match your CNI plugin (Flannel uses 10.244.0.0/16) — defines pod IP range 🗂️
--upload-certs                                       ,Shares certificates securely with worker nodes — no manual copying! 🧾













