 Kubernetes Single Node Cluster Setup on Ubuntu (with Calico)

> Tested on Ubuntu 22.04 (WSL or VM)
> Kubernetes version: `v1.30.x`
> Pod Network: [Calico](https://docs.tigera.io/calico/latest/)

---

#### 📌 Pre-Requisites

Ensure your system is updated and `br_netfilter` module is loaded:

```bash
sudo apt update && sudo apt upgrade -y
sudo modprobe br_netfilter
lsmod | grep br_netfilter
```

Enable required kernel params:

```bash
sudo tee /etc/modules-load.d/k8s.conf <<EOF
br_netfilter
EOF

sudo tee /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system
```

---

### 🧰 Step-by-Step Installation

---

#### 1. Remove old sources (if any)

```bash
sudo rm -f /etc/apt/sources.list.d/kubernetes.list
sudo rm -f /usr/share/keyrings/kubernetes-archive-keyring.gpg
```

---

#### 2. Add Kubernetes v1.30 repository

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | \
sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update
```

---

#### 3. Install required packages

```bash
sudo apt install -y kubelet kubeadm kubectl conntrack cri-tools ethtool socat
```

Pin the versions (to prevent automatic upgrades):

```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

---

#### 4. Initialize Kubernetes Cluster (Control Plane Only)

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

📋 After successful init, copy and run the output commands:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

#### 5. Install Calico CNI Plugin

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
```

Wait 30-60 seconds, then confirm:

```bash
kubectl get pods -n kube-system
kubectl get nodes
```

You should see the node status as `Ready`.

---

### ✅ Troubleshooting Notes

* If Calico pods are stuck in `PodInitializing`, check logs using:

```bash
kubectl logs -n kube-system -l k8s-app=calico-node
```

* Make sure your kernel modules and system configs are properly set for networking.

* If Calico still fails, reapply the YAML:

```bash
kubectl delete -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
```

---

### 🚀 Final Check

```bash
kubectl get nodes
kubectl get pods -A
```

Everything should now be `Running` and `Ready`.

---

### 📦 To Add Worker Nodes

Run this (example output from kubeadm init):

```bash

kubeadm join 172.30.178.85:6443 --token baeaz1.wdu635eprhbzzwez \
        --discovery-token-ca-cert-hash sha256:5fafea6867d80c4cac1c16fff06ada9c9e58c65000a968d8d6f19eefa0d27d72  
```
-its my output 
> You can copy this from the `kubeadm init` output.

