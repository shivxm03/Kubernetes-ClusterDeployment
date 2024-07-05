# Kubernetes Cluster Deployment on EC2 Instances Using Kubeadm

In the realm of modern cloud computing, Kubernetes has emerged as a game-changer, revolutionizing the way we deploy, manage, and scale containerized applications. Its robust features and scalability make it a top choice for orchestrating containerized workloads. In this guide, we’ll walk you through the process of deploying a Kubernetes cluster on Amazon EC2 instances using Kubeadm, a popular tool for bootstrapping Kubernetes clusters.

## Why EC2 and Kubeadm?

Amazon EC2 (Elastic Compute Cloud) provides resizable compute capacity in the cloud. It’s an ideal choice for Kubernetes cluster setup and deployment due to its flexibility, scalability, and reliability. Kubeadm automates many of the manual processes involved in the setup of a Kubernetes cluster on AWS EC2, making it a popular choice for beginners and experts alike.

## Prerequisites

Before diving into the deployment process, ensure you have the following prerequisites in place:
- An AWS account with appropriate permissions to create EC2 instances.
- AWS CLI installed and configured on your local machine.

## Create Deployment of Kubernetes Cluster

### Step 1: Set Up EC2 Instances

Using the AWS Management Console or AWS CLI, launch three EC2 instances: one for the master node and two for the worker nodes. Ensure that the AWS EC2 instances meet the minimum requirements for running Kubernetes, including sufficient CPU, memory, and network connectivity.

**Minimum Requirements:**
- **Master Node:** `t2.medium` recommended
  - CPU: 2 vCPUs or more
  - RAM: 4 GB or more
  - Disk: 20 GB or more
  - Network: Sufficient bandwidth for communication between nodes and with external services
- **Worker Nodes:**
  - CPU: 1 vCPU or more (depending on workload)
  - RAM: 2 GB or more
  - Disk: 20 GB or more
  - Network: Sufficient bandwidth for communication between nodes and with external services

![Screenshot 2024-07-05 050421](https://github.com/shivxm03/Kubernetes-ClusterDeployment/assets/157244434/52d78527-3a9a-44a8-88b1-54641f41ed3f)

  

### Step 2: Install Docker and Kubeadm

SSH into each Amazon EC2 instance and install Docker and Kubeadm.

**Install Docker:**

```bash
#!/bin/bash
sudo su
apt-get update
apt-get install -y apt-transport-https
apt install -y docker.io
docker --version
systemctl start docker
systemctl enable docker
```
Install Kubeadm, Kubelet, and Kubectl:

```bash
#!/bin/bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet
```

Step 3: Initialize the Kubernetes Cluster
On the master node, initialize the Kubernetes cluster using Kubeadm. Run the following command:

```bash
sudo kubeadm init
```

Next, set up kubectl for the regular user:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Step 4: Set Up Networking
To enable pod networking within the cluster, install a CNI (Container Network Interface) plugin. Here we use Flannel:

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/v0.22.2/Documentation/k8s-old-manifests/kube-flannel-rbac.yml
```

Step 5: Join Worker Nodes to the Cluster
Once the master node initialization is complete, you’ll receive a kubeadm join command with a token. SSH into each worker node and run the kubeadm join command to join them to the cluster.

![Screenshot 2024-07-05 050335](https://github.com/shivxm03/Kubernetes-ClusterDeployment/assets/157244434/cad502b2-f5ba-49b2-9430-b3028b28960c)

Step 6: Verify the Cluster
To verify that the cluster is up and running, SSH into the master node and run the following command:

```bash
kubectl get nodes
```

***Conclusion**
Congratulations! You have successfully deployed a Kubernetes cluster on EC2 instances using Kubeadm. You can now start deploying and managing your containerized applications on this cluster.

