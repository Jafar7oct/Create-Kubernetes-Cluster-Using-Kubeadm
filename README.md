# 🧠 Create-Kubernetes-Cluster-Using-Kubeadm

Hey there, cloud wizard! 🧙‍♂️  
This repo will help you **create your own Kubernetes cluster** locally using **Vagrant + VirtualBox + Kubeadm**.  
Perfect for DevOps learners, cloud engineers, and anyone who loves containers more than coffee ☕🐳

---

## ⚙️ Prerequisites

Before we start the magic 🪄, make sure you have these tools installed on your machine:

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

2️⃣ Enter the Project Folder
bash
Copy code
cd Create-Kubernetes-Cluster-Using-Kubeadm
👉 Why?
We need to be inside the directory that contains the Vagrantfile — that’s where the magic happens 🪄.

3️⃣ Launch Your Cluster VMs!
bash
Copy code
vagrant up
👉 What it does:
Starts creating and configuring all VMs defined in the Vagrantfile (Control Plane + Worker Nodes).
Go grab a snack 🍕 — it might take a few minutes ⏳

4️⃣ Check VM Status
bash
Copy code
vagrant status
👉 Why?
To make sure all your VMs are running and healthy 💪

🧹 Other Useful Vagrant Commands
Command	What it does	Emoji
vagrant halt	Stops all running VMs (like pausing Netflix 🎬)	🛑
vagrant destroy	Removes all VMs completely (clean slate 💣)	🔥
vagrant reload	Reboots VMs and re-applies changes to the Vagrantfile	♻️
vagrant ssh <vm-name>	Connects to a specific VM (like remote login)	🧑‍💻

5️⃣ SSH into the Machines
bash
Copy code
vagrant ssh controlplane
vagrant ssh node01
vagrant ssh node02
👉 Why?
We’ll configure Kubernetes manually using kubeadm on these machines.

🏗️ Install and Configure Prerequisites (Inside Each VM)
🚫 Disable Swap
bash
Copy code
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
👉 Why?
Kubernetes hates swap — it messes with resource management.

🌐 Enable IPv4 Packet Forwarding
bash
Copy code
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
EOF
sudo sysctl --system
sysctl net.ipv4.ip_forward
👉 Why?
This lets your cluster nodes communicate and forward packets properly.
Without it, pods can’t talk — and that’s sad 😢.

📦 Install Kubernetes Tools
bash
Copy code
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
👉 Why?
These are the 3 amigos of Kubernetes:

kubelet: Runs on every node and talks to the control plane 🗣️

kubeadm: Bootstraps your cluster 🎯

kubectl: The boss command-line tool 🕹️

🐳 Install and Configure containerd (Container Runtime)
bash
Copy code
sudo apt install -y containerd
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml
👉 Why?
Kubernetes needs a container runtime to run containers.
Containerd is fast, lightweight, and official 🏎️

🧩 Edit config.toml for systemd cgroup driver
Open /etc/containerd/config.toml and make sure this part looks like:

toml
Copy code
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
  SystemdCgroup = true
👉 Why?
Kubernetes prefers systemd for managing cgroups — smoother and more stable 🧘‍♂️.

🔁 Restart and Enable containerd
bash
Copy code
sudo systemctl restart containerd
sudo systemctl enable containerd
🧠 Initialize the Control Plane
bash
Copy code
sudo kubeadm init --apiserver-advertise-address <CONTROLPLANE_IP> \
--pod-network-cidr=10.244.0.0/16 --upload-certs
👉 What those mean:

--apiserver-advertise-address: IP of your control plane (master node).

--pod-network-cidr: Must match your CNI plugin (Flannel → 10.244.0.0/16).

--upload-certs: Shares certificates with worker nodes securely 🧾.

🧰 Configure kubectl for Your User
bash
Copy code
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
👉 Why?
So you can use kubectl without being root 👑

🕸️ Install Flannel CNI (Networking)
bash
Copy code
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
kubectl get pods -n kube-flannel --watch
👉 Why?
Flannel gives pods their own internal network to communicate 🛜

🧯 Fixing CNI Issues (if pods crash 😭)
bash
Copy code
sudo modprobe br_netfilter
lsmod | grep br_netfilter
Create or fix sysctl config:

bash
Copy code
sudo tee /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
sudo sysctl --system
Then restart the Flannel pods:

bash
Copy code
kubectl delete pod -n kube-flannel --all
kubectl get pods -n kube-flannel --watch
⚡ Add Worker Nodes
Run this on each worker node:

bash
Copy code
sudo kubeadm join <CONTROLPLANE_IP>:6443 --token <TOKEN> \
--discovery-token-ca-cert-hash sha256:<HASH>
👉 Why?
This securely connects your worker nodes to the control plane.
Think of it as “joining the Kubernetes party 🎉”.

✅ Verify the Cluster
bash
Copy code
kubectl get nodes
kubectl get pods -A
kubectl get pods -n kube-flannel
If everything looks green 🟢 → Congratulations! 🎊
You’ve just built your very own Kubernetes cluster from scratch 💪🚀

💡 Pro Tips
Use vagrant snapshot save <name> to save VM state 🧩

Use kubectl get events --sort-by=.metadata.creationTimestamp to debug issues 🕵️‍♂️

Need to reset everything?

bash
Copy code
sudo kubeadm reset -f
sudo systemctl restart containerd
🧡 Credits
Made with ☕, 🐧, and a bit of YAML magic ✨
by Ja’far Khalaf

👋 Thanks for visiting — don’t forget to ⭐ the repo if you learned something new!

yaml
Copy code

---

Would you like me to **add a “Project Architecture Diagram”** (VMs + Network + Kubernetes roles) in the README using Markdown + ASCII art?  
It would make your repo look even more professional and beginner-friendly 🚀
