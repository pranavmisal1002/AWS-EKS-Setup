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

