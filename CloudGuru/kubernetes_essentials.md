# Kubernetes Essentials

https://interactive.linuxacademy.com/diagrams/KubernetesEssentialsInteractiveDiagram.html

### Kubernetes Management Components

Kubernetes has multiple components that are either required, or assist in setting up, managing and running the cluster:

- **Control plane Components**
  - Components that are responsible for managing and controlling the cluster;
  - **etcd** - Provides distributed, synchronized data storage for the cluster state;
  - **kube-apiserver** - This API is the primary interface for the cluster;
    - Client facing interface;
  - **kube-controller-manager** - Bundles several components into one package for handling the backend administration of the cluster;
  - **kube-scheduler** - Schedules pods to run on individual nodes;
- **Components that all nodes have**
  - **kubelet** - Agent that servers as a middleman between Kubernetes and the Container Runtime;
    - Handles running containers on a node;
    - This one is a service and not a pod, like the previous ones;
  - **kube-proxy** - Handles network communication between nodes by adding firewall routing rules;
- Components that assist in managing the cluster
  - **kubeadm**
    - Tool that automates a large portion of the process of setting up a cluster;
    - Allows for many of these components to run as pods within the cluster itself;
  - **kubectl**
    - Command-line tool for interacting with the cluster;

More components or a detailed view of the ones presented here, can be found in [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/).



### Containers and Pods

**Pods** are the smallest and most basic building block of the Kubernetes model.

A **Pod** consists of one or more containers (usually only one, but if there are more, they are tightly coupled containers/applications), storage resources, and a unique IP address in the Kubernetes cluster network.

In order to run containers, Kubernetes **schedules** pods to run on servers in the cluster. When a pod is scheduled, the server will run the containers that are part of that pod.



### Clustering and Nodes

Kubernetes implements a **clustered** architecture, where multiple **nodes** compose the cluster.

These nodes in the cluster are either

- **control nodes**
  - Manage and control the cluster and host the **Kubernetes API**;
- **data nodes**
  - Nodes where the pods run;



### Lab Environment

Composed of three servers with the following software:

- One master node;
  - Docker - Container Runtime;
  - Kubeadm - Tool that facilitates the process of setting up a Kubernetes cluster;
  - Kubelet - Agent that manages the containers on each node;
  - Kubectl - Kubernetes Command Line Interface;
  - Control Plane - Services that forms the Kubernetes Master;
- Two data nodes;
  - Docker
  - Kubeadm
  - Kubelet
  - Kubectl

#### Playground

Used Cloud Playground and created the following items:

- Kube-Master
  - Ubuntu Server 18.04 Bionic Beaver LTS
  - Size Small
- Kube-Data01
  - Ubuntu Server 18.04 Bionic Beaver LTS
  - Size Small
- Kube-Data02
  - Ubuntu Server 18.04 Bionic Beaver LTS
  - Size Small
- Changed the hostname on each server to match their name;
  - `sudo hostnamectl set-hostname "new-hostname"`
  - `sudo hostnamectl set-hostname "new-hostname" --pretty`



#### Install Docker

After these items were setup, the next step was to install Docker, as the runtime container image for the Kubernetes cluster:

```bash
# Add the Docker GPG Key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Add the Docker Repo
sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"

# Install Docker
sudo apt-get update
sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu

# Maintain Docker in the version installed
sudo apt-mark hold docker-ce

# Check Docker is installed
docker --version
```

> **NOTE**: Kubernetes recommends that Docker use the systemd driver as it's default cgroup driver.
>
> https://kubernetes.io/docs/setup/production-environment/container-runtimes/#cgroup-drivers
>
> In order to do this, the following commands need to be executed:

```bash
# Alter Docker's default cgroup driver
cat << EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}

EOF

# Restart Docker
sudo systemctl restart docker
```



#### Install Kubernetes Components

```bash
# Add Kubernetes GPG Key
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

# Add the Repository
cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

# Reload the apt source list
sudo apt-get update

# Install Kubelet
# Needed in every node
sudo apt-get install -y kubelet=1.15.7-00
sudo apt-mark hold kubelet
# Install Kubeadm and Kubectl
# Only needed in a management node
sudo apt-get install -y kubeadm=1.15.7-00 kubectl=1.15.7-00
sudo apt-mark hold kubeadm kubectl

# Check if its installed
kubeadm version
```



#### Bootstrap the Cluster

```bash
# On the Kube master node, initialize the cluster:
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

# When it is done, set up the local kubeconfig:
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Verify that the cluster is responsive and that Kubectl is working:
kubectl version

# The kubeadm init command should output a kubeadm join command containing a token and hash.
# Copy that command and run it with sudo on both worker nodes. It should look something like this:
# NOTE: THIS COMMAND WILL BE PRINTED IN THE MASTER CONSOLE
# COPY AND PASTE IT TO THE DATA NODES
sudo kubeadm join $some_ip:6443 --token $some_token --discovery-token-ca-cert-hash $some_hash

# Verify that all nodes have successfully joined the cluster by running the following command on the master:
kubectl get nodes

# NOTE: The nodes are expected to have a STATUS of NotReady at this point.
```



#### Configure Cluster Networking

When running Kubernetes, there is also a system configuration that needs to be performed, in order for the containers to be able to communicate correctly between Kubernetes Nodes.

This configuration is activating the `net.bridge.bridge-nf-call-iptables`. This family of three flags "*control whether or not packets traversing the...* " (Linux Host)  "*...bridge are sent to iptables for processing*".

More information, like the quote presented bellow, can be found in this document from **libvirt**:

> These control **whether or not packets traversing the bridge are sent to iptables for processing**. In the case of using bridges to connect virtual machines to the network, generally such processing is *not* desired, as it results in guest traffic being blocked due to host iptables rules that only account for the host itself, and not for the guests.
> *From:* https://wiki.libvirt.org/page/Net.bridge.bridge-nf-call_and_sysctl.conf

*A more detailed explanation on how this works and why it's needed can be found in the following article: https://programmer.ink/think/61b20fd736d13.html*.

So to configure this, just perform the following command in all Kubernetes Nodes:

```bash
# Set the flag to be active on next startup
echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
# Activate the flag before next startup
sudo sysctl -p
```



After dealing with the iptables issue, we can focus on Kubernetes, which has 4 distinct networking problems to address:

1. Highly-coupled **container-to-container** communications: this is solved by [Pods](https://kubernetes.io/docs/concepts/workloads/pods/) and `localhost` communications.
2. **Pod-to-Pod** communications;
3. **Pod-to-Service** communications;
4. **External-to-Service** communications;

With this said, one aspect of Kubernetes networking, which is the **Pod-to-Pod** one, needs to be defined in the creation of the cluster. This communication requires the creation of a **virtual cluster network**, that spans all nodes and where every pod has an unique IP address

In this Playground example, the [Flannel project](https://github.com/flannel-io/flannel#flannel) was used, but a [bunch more are available to be used](https://kubernetes.io/docs/concepts/cluster-administration/networking/).

To configure Flannel, we just add to apply the [Flannel YAML configuration](https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml) file, that the maintainers of Flannel have created.

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

After this is configured all of the nodes should be *Ready* and *Flannel Pods* should appear in the `kube-system` *Pods*:

```bash
# Check the cluster nodes
# This might take a few moments after you aplied the Flannel config
# 	before the Nodes appear ready. Just give it a few seconds.
kubectl get nodes

# Check for the Flannel Pods
kubectl get pods -n kube-system
```



#### Containers and Pods

Create and schedule a simple pod running a nginx container:

```bash
# Create the nginx pod
cat << EOF | sudo tee ~/kubernetes/example_pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
EOF

# Schedule the pod
kubectl create -f ~/kubernetes/example_pod.yml

# Get list of pods that are running
kubectl get pods

# Get list of system pods, by specifing the kube-system namespace
kubectl get pods -n kube-system

# Get more information on the pod
kubectl describe pod nginx

# Delete the pod
kubectl delete pod nginx
```



#### Clustering and Nodes

To get more information on the cluster or a specific node you can use the following command:

```bash
# Get list of cluster nodes
kubectl get nodes

# Get more detailed information on a specific node
kubectl describe node kube-data01
```



### Random Notes

In case a Kubernetes cluster node needs to be reverted, just perform the `sudo kubeadm reset` command.

Kubernetes objects have all an associated **namespace**.



### CloudGuru Labs

#### Building a Kubernetes 1.23 Cluster with kubeadm

##### Introduction

This lab will allow you to practice the process of building a new Kubernetes cluster. You will be given a set of Linux servers, and you will have the opportunity to turn these servers into a functioning Kubernetes cluster. This will help you build the skills necessary to create your own Kubernetes clusters in the real world.

##### Solution

###### Install Packages

```bash
# 1. Log into the Control Plane Node *(Note: The following steps must be performed on all three nodes.).*

# 2. Create configuration file for containerd:
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

# 3. Load modules:
sudo modprobe overlay
sudo modprobe br_netfilter

# 4. Set system configurations for Kubernetes networking:
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

# 5. Apply new settings:
sudo sysctl --system

# 6. Install containerd:
sudo apt-get update && sudo apt-get install -y containerd

# 7. Create default configuration file for containerd:
sudo mkdir -p /etc/containerd

# 8. Generate default containerd configuration and save to the newly created default file:
sudo containerd config default | sudo tee /etc/containerd/config.toml

# 9. Restart containerd to ensure new configuration file usage:
sudo systemctl restart containerd

# 10. Verify that containerd is running.
sudo systemctl status containerd

# 11. Disable swap:
sudo swapoff -a

# 12. Disable swap on startup in
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# 13. Install dependency packages:
sudo apt-get update && sudo apt-get install -y apt-transport-https curl

# 14. Download and add GPG key:
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

# 15. Add Kubernetes to repository list:
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

# 16. Update package listings:
sudo apt-get update

# 17. Install Kubernetes packages (Note: If you get a dpkg lock message, just wait a minute or two before trying the command again):
sudo apt-get install -y kubelet=1.23.0-00 kubeadm=1.23.0-00 kubectl=1.23.0-00

# 18. Turn off automatic updates:
sudo apt-mark hold kubelet kubeadm kubectl

# 19. Log into both Worker Nodes to perform previous steps.
```



###### Initialize the Cluster

```bash
# 1. Initialize the Kubernetes cluster on the control plane node using kubeadm (Note: This is only performed on the Control Plane Node):
sudo kubeadm init --pod-network-cidr 192.168.0.0/16 --kubernetes-version 1.23.0

# 2. Set kubectl access:
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# 3. Test access to cluster:
kubectl get nodes
```



###### Install the Calico Network Add-On

```bash
# 1. On the Control Plane Node, install Calico Networking:
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

# 2. Check status of the control plane node:
kubectl get nodes
```



###### Join the Worker Nodes to the Cluster

```bash
# 1. In the Control Plane Node, create the token and copy the kubeadm join command (NOTE:The join command can also be found in the output from *kubeadm init* command):
kubeadm token create --print-join-command

# 2. In both Worker Nodes, paste the kubeadm join command to join the cluster. Use sudo to run it as root:
sudo kubeadm join ...

# 3. In the Control Plane Node, view cluster status (Note: You may have to wait a few moments to allow all nodes to become ready):
kubectl get nodes
```

