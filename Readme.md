# 02-Service Mesh with HashiCorp Consul (10%)
## Demo project accompanying a [Consul crash course video](https://www.youtube.com/watch?v=s3I1kKKfjtQ) on YouTube by TechWorld with Nana

## Tools:
| Tool        | What it Does |
|-------------|--------------|
| [Consul](https://developer.hashicorp.com/consul/docs/intro) | Controls service-to-service communication, finds services (service discovery), secures communication with mTLS, and handles failover to healthy services. |
| [Helm](https://helm.sh/docs/) | Installs applications on Kubernetes easily with charts. Manages installation, upgrades, and rollbacks. |
| [Linode](https://www.linode.com/) | Provides cloud servers and Kubernetes clusters. Used to create a second Kubernetes cluster. |
| [AWS Service](https://aws.amazon.com/) | Provides cloud services including EKS (Elastic Kubernetes Service) for running Kubernetes clusters. Used to create the first Kubernetes cluster. |
| [Terraform](https://aws.amazon.com/) | Automates the creation of infrastructure like servers, Kubernetes clusters, and networks with code. Manages resources safely and efficiently. |
| [Kubernetes](https://kubernetes.io/) | Runs and manages containerized applications. Handles scaling, healing, and updating applications. Used to deploy microservices and Consul. |

## Some Screenshots of the application
![Screenshot 2025-04-27 151204](https://github.com/user-attachments/assets/457437cc-e4cd-4abf-9d6e-a5577579ef69)
![Screenshot 2025-04-27 151215](https://github.com/user-attachments/assets/7ec97538-37a7-47a8-9108-9d2c18087a13)
![Screenshot 2025-04-27 151956](https://github.com/user-attachments/assets/4cfbd3a5-96a2-45b8-bd34-9cd4369524e2)

---

# Setup Instructions

## 1. Clone the Repository

Clone the project repository to local machine:

```bash
git clone https://github.com/thorungb/Service-Mesh-with-HashiCorp-Consul.git
```

## 2. Create the `terraform.tfvars` File

Inside the `terraform/` directory, create a file named `terraform.tfvars` and add your AWS credentials:

```hcl
aws_access_key_id     = "YOUR_AWS_ACCESS_KEY_ID"
aws_secret_access_key = "YOUR_AWS_SECRET_ACCESS_KEY"
```

### Required AWS Permissions

Ensure the provided credentials have access to the following AWS services:

- EC2 (VPCs, Subnets, Security Groups)
- EKS (Clusters, Node Groups)
- IAM (Roles, Policies)
- CloudWatch Logs

**Note:** If unsure about permissions, attach the `AdministratorAccess` policy to the user.

---

# Deploy Kubernetes Cluster on AWS EKS

## 1. Initialize Terraform

Navigate to the `terraform` directory and initialize Terraform:

```bash
cd terraform
terraform init
```

## 2. Apply Terraform Configuration

Apply the Terraform configuration using the variables file:

```bash
terraform apply -var-file terraform.tfvars
```

This will create an EKS cluster with three nodes in the `ap-southeast-1` region (region is configurable in `variables.tf`).

## 3. Configure AWS CLI

Configure the AWS CLI with your credentials:

```bash
aws configure
```

## 4. Generate Kubeconfig

Update your Kubernetes configuration file to access the new EKS cluster:

```bash
aws eks update-kubeconfig --region ap-southeast-1 --name myapp-eks-cluster
```

## 5. Verify Cluster Access

Check that the nodes are running:

```bash
kubectl get nodes
```

You should see three nodes listed.

---

# Deploy Microservices Application

Navigate to the `kubernetes/` directory and apply the Kubernetes configuration:

```bash
kubectl apply -f config.yaml
```

Verify that the pods are running:

```bash
kubectl get pods
```

---

# Deploy Consul on EKS

## 1. Install Helm

Install Helm. For example, using Chocolatey on Windows:

```bash
choco install kubernetes-helm
```

Alternatively, follow the [Helm installation guide](https://helm.sh/docs/intro/install/).

## 2. Install Consul

Add the HashiCorp Helm repository and install Consul:

```bash
helm repo add hashicorp https://helm.releases.hashicorp.com
helm install eks hashicorp/consul --version 1.0.0 --values consul-values.yaml --set global.datacenter=eks
```

## 3. Verify Consul Pods

Check that the Consul pods are running:

```bash
kubectl get pods
```

You should see pods named `eks-consul-*` in a running state.

---

# Deploy Kubernetes Cluster on Azure AKS

## 1. Connect to the AKS Cluster

Retrieve the AKS cluster credentials:

```bash
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

This command merges the AKS kubeconfig into your `~/.kube/config` file.

## 2. Verify Cluster Access

Check the AKS cluster nodes:

```bash
kubectl get nodes
```

## 3. Install Consul on AKS

Install Consul in the AKS cluster:

```bash
helm install aks hashicorp/consul --version 1.0.0 --values consul-values.yaml --set global.datacenter=aks
```

## 4. Verify Consul Pods

Ensure that the Consul pods are running:

```bash
kubectl get pods
```

---

# Connect the EKS and AKS Clusters

In each cluster, apply the mesh gateway configuration:

```bash
kubectl apply -f consul-mesh-gateway.yaml
```

Switch the Kubernetes context between clusters and repeat the command.

Connect the clusters using the External IPs of the Consul UI services.

---

# Destroy AWS Resources

When finished, destroy the AWS resources created by Terraform:

```bash
cd terraform
terraform destroy
```
