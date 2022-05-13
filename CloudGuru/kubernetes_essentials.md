# Kubernetes Essentials

https://interactive.linuxacademy.com/diagrams/KubernetesEssentialsInteractiveDiagram.html

### Kubernetes Management Components

Kubernetes has the following 3 components that are either required, or assist in setting up and managing the cluster:

- **Kubeadm**
  - Tool that automates a large portion of the process of setting up a cluster;
- **Kubelet**
  - Handles running containers on a node;
  - Agent that servers as a middleman between Kubernetes and the Container Runtime;
  - Every node needs kubelet;
- **Kubectl**
  - Command-line tool for interacting with the cluster;

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

