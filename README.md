# Create-Kubernetes-Cluster-Using-Kubeadm

## Install and configure prerequisites

sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

Enable IPv4 packet forwarding
To manually enable IPv4 packet forwarding:

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
Verify that net.ipv4.ip_forward is set to 1 with:

sysctl net.ipv4.ip_forward

sudo apt-get update
### apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

### If the directory `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
### sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg


### This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl



sudo apt install -y containerd
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml


تعديل config.toml لاستخدام systemd cgroup driver
لـ containerd 1.x:
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
  SystemdCgroup = true



sudo systemctl restart containerd
sudo systemctl enable containerd




تهيئة الـ control plane
sudo kubeadm init --apiserver-advertise-address <CONTROLPLANE_IP> \
--pod-network-cidr=10.244.0.0/16 --upload-certs


--apiserver-advertise-address → IP الخاص بالـ master/control plane.
--pod-network-cidr → يجب أن يطابق شبكة الـ CNI (Flannel → 10.244.0.0/16).
--upload-certs → يسمح بتوزيع الشهادات على الـ worker nodes






mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config




kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml

kubectl get pods -n kube-flannel --watch


6️⃣ حل مشاكل CNI (CrashLoopBackOff / Error)
التأكد من تحميل br_netfilter:
sudo modprobe br_netfilter
lsmod | grep br_netfilter


إنشاء ملف sysctl إذا لم يكن موجود:
sudo tee /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system


kubectl delete pod -n kube-flannel --all
kubectl get pods -n kube-flannel --watch



إضافة الـ worker nodes

على كل Node:

sudo kubeadm join <CONTROLPLANE_IP>:6443 --token <TOKEN> \
--discovery-token-ca-cert-hash sha256:<HASH>


يجب استخدام sudo (root user).

إذا ظهر خطأ ip_forward أو br_netfilter، طبق نفس إعداد sysctl من الخطوة 6.




kubectl get nodes
kubectl get pods -A
kubectl get pods -n kube-flannel



