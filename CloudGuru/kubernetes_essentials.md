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

Used AWS Playground and created the following items:

- A Subnet named Public_Subnet01;
- A Route Table named Public_RT;
- A NACL called FullAccess_NACL;
- A Security Group called FullAccess_SG;
- Three EC2 instances:
  - Kube-Master01
    - Ubuntu Server 22.04
    - T2.small
  - Kube-Data01
    - Ubuntu Server 22.04
    - T2.small
  - Kube-Data02
    - Ubuntu Server 22.04
    - T2.small
  - Changed the hostname on each server to match their name;
    - `hostnamectl set-hostname "new-hostname"`
    - `hostnamectl set-hostname "new-hostname" --pretty`



After these items were setup, the next step was to install Docker, as the runtime container image for the Kubernetes cluster:

```bash
# Add the Docker GPG Key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Add the Docker Repo
sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"

# Install Docker
sudo apt-get update
sudo apt-get install -y docker-ce

# Maintain Docker in the version installed
sudo apt-mark hold docker-ce

# Check Docker is installed
docker --version
```



Next step was to install the `cri-dockerd` to provide a *shim* for *Docker Engine* that lets you control *Docker* via the *Kubernetes* cluster:

```bash
# https://github.com/Mirantis/cri-dockerd
# Instructions for installation from the site

# Download cri-dockerd
wget https://github.com/Mirantis/cri-dockerd/archive/refs/tags/v0.2.0.tar.gz

# Extract the code
mkdir cri-dockerd
tar -xf v0.2.0.tar.gz -C cri-dockerd
mv cri-dockerd/cri-dockerd-0.2.0/ .
rm v0.2.0.tar.gz
rm -rf cri-dockerd/

# Install Go
sudo apt-get install -y golang-go

# Build cri-dockerd
cd cri-dockerd-0.2.0/
mkdir bin
cd src && go get && go build -o ../bin/cri-dockerd
cd ..

# Make sure the bin folder exists
sudo mkdir -p /usr/local/bin
# Install cri-dockerd
sudo install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
# Copy the services
sudo cp -a packaging/systemd/* /etc/systemd/system
sudo sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
# Start the services
sudo systemctl daemon-reload
sudo systemctl enable cri-docker.service
sudo systemctl enable --now cri-docker.socket
sudo systemctl start cri-docker.service

```



Now to install Kubernetes:

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
sudo apt-get install -y kubelet
sudo apt-mark hold kubelet
# Install Kubeadm and Kubectl
# Only needed in a management node
sudo apt-get install -y kubeadm kubectl
sudo apt-mark hold kubeadm kubectl

# Check if its installed
kubeadm version
```

