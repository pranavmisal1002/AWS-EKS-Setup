# ✅ Proper Steps to Create AWS EKS Cluster (Manual + CLI Access)

This section explains how to create an **Amazon EKS cluster in Custom mode** with **Auto Mode disabled**, so you can manage networking, node groups, and configuration manually.

---

## 1) Create EKS Cluster (Custom Mode)

1. Open **AWS Console**
2. Search **EKS** → Go to **Amazon EKS**
3. Click **Clusters** → **Create cluster**
4. Select the following options:
   - **Configuration:** Custom
   - **Auto mode:** OFF (Disable Auto Mode)
## 2) Create IAM Role for EKS Cluster

Create an IAM role that allows Amazon EKS to manage cluster resources.

1. Open a new tab → **IAM** → **Roles** → **Create role**
2. Select:
   - **Trusted entity:** AWS service
   - **Service:** EKS
   - **Use case:** EKS - Cluster
3. Attach Policy:
   - ✅ `AmazonEKSClusterPolicy`
4. Role Name:
   - `eks-cluster-role`
5. Click **Create role**
## 3) Complete Cluster Configuration

Go back to the **EKS Cluster Create Page** and complete the cluster configuration.

### Cluster Details
1. Enter the following:
   - **Cluster name:** `my-eks-cluster` (example)
   - **Kubernetes version:** Latest / Stable
   - **Cluster service role:** Select `eks-cluster-role`

### Networking
2. Configure networking:
   - Choose your **VPC**
   - Select **Subnets** (minimum **2 subnets** in **2 different AZs**)
   - Select/Create a **Security Group**

### Additional Security Group (Optional)
3. Add an additional security group if required.

### Create Cluster
4. Click **Create**

⏳ Wait around **10 minutes** for the cluster status to become **Active**.

# ✅ Create Worker Node (Node Group)

## 4) Create IAM Role for Worker Node

Create an IAM role that allows the worker nodes (EC2 instances) to join and operate inside the EKS cluster.

1. Go to **IAM** → **Roles** → **Create role**
2. Select:
   - **Trusted entity:** AWS service
   - **Service:** EC2
3. Attach the following policies:
   - ✅ `AmazonEKSWorkerNodePolicy`
   - ✅ `AmazonEKS_CNI_Policy`
   - ✅ `AmazonEC2ContainerRegistryReadOnly`
4. Role name:
   - `eks-node-role`
5. Click **Create role**

## 5) Create Node Group (Worker Node Group)

Create a worker node group so EC2 worker nodes can join your EKS cluster.

1. Open **Amazon EKS**
2. Click your **Cluster name**
3. Navigate to:
   - **Compute** → **Add node group / Create node group**
4. Fill in node group details:
   - **Node group name:** `worker-node-group`
   - **Node IAM role:** Select `eks-node-role`
5. Click **Next**
6. Choose instance type (example):
   - `t3.medium`
7. Configure scaling (example):
   - **Desired:** 2
   - **Min:** 1
   - **Max:** 3
8. Click:
   - **Next → Next → Create**

✅ **Node group will be created and worker nodes will join the cluster automatically.**

## Launch EC2 Instance Install kubectl on Linux (Ubuntu)

Launch an EC2 instance that will host both the **Install kubectl on Linux (Ubuntu)**.

1. Go to **EC2** → **Launch Instance**
2. Instance Name:
   - `Master-Node`
3. AMI:
   - Ubuntu `22.04`
4. Instance Type:
   - `c7i-flex.large` *(change according to backend load)*
5. Storage:
   - `20 GB` *(increase based on application requirements)*
6. Security Group (Inbound Rules):
   - Allow `22` (SSH)
   - Allow `8080` (Backend)
   - Allow `80` (Frontend / Apache)

Click **Launch Instance** ✅

---

#  Connect to EC2 & Install

## Install kubectl on Linux (Ubuntu)

### 6) Download kubectl

Download the latest stable version of `kubectl`:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
### 7) Validate the kubectl Binary

Download the checksum file:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```
Verify the checksum:

```bash
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

### 8) Install kubectl

Install `kubectl` to `/usr/local/bin` with correct permissions:

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
✅ Verify installation:
```bash
kubectl version --client
```
> **Note (Optional): Install kubectl without root access**

If you do not have root permissions, you can install `kubectl` in your local user directory:

```bash
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
```
##  Install AWS CLI & Configure

### 9) Install AWS CLI

Install AWS CLI on Ubuntu using Snap:

```bash
sudo snap install aws-cli --classic
```
✅ Verify installation:
```bash
aws --version
```
### 10) Configure AWS CLI

Configure AWS CLI with your AWS credentials:

```bash
aws configure
```
Enter the following details when prompted:

- **AWS Access Key**
- **AWS Secret Key**
- **Default region name:** `ap-south-1` (example)
- **Default output format:** `json`
## ✅ Login / Connect to EKS Cluster

### 11) Update kubeconfig

Configure your local kubeconfig to connect with the EKS cluster:

```bash
aws eks update-kubeconfig --name <your_eks_cluster_name> --region <your-region>
```
Example:
```bash
aws eks update-kubeconfig --name my-eks-cluster
```
### 12) Verify Cluster Connection

Run the following commands to confirm you are connected to the EKS cluster:

```bash
kubectl get nodes
kubectl get pods -A
```

##✅ If nodes show Ready, your EKS setup is successful 🎉

