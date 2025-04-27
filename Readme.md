# 02-Service Mesh with HashiCorp Consul (10%)
## Demo project accompanying a [Consul crash course video](https://www.youtube.com/watch?v=s3I1kKKfjtQ) on YouTube by TechWorld with Nana
### Tools:
| Tool        | What it Does |
|-------------|--------------|
| [Consul](https://developer.hashicorp.com/consul/docs/intro) | Controls service-to-service communication, finds services (service discovery), secures communication with mTLS, and handles failover to healthy services. |
| [Helm](https://helm.sh/docs/) | Installs applications on Kubernetes easily with charts. Manages installation, upgrades, and rollbacks. |
| [Linode](https://www.linode.com/) | Provides cloud servers and Kubernetes clusters. Used to create a second Kubernetes cluster. |
| [AWS Service](https://aws.amazon.com/) | Provides cloud services including EKS (Elastic Kubernetes Service) for running Kubernetes clusters. Used to create the first Kubernetes cluster. |
| [Terraform](https://aws.amazon.com/) | Automates the creation of infrastructure like servers, Kubernetes clusters, and networks with code. Manages resources safely and efficiently. |
| [Kubernetes](https://kubernetes.io/) | Runs and manages containerized applications. Handles scaling, healing, and updating applications. Used to deploy microservices and Consul. |


### Some Screenshots of application
![Screenshot 2025-04-27 151204](https://github.com/user-attachments/assets/457437cc-e4cd-4abf-9d6e-a5577579ef69)
![Screenshot 2025-04-27 151215](https://github.com/user-attachments/assets/7ec97538-37a7-47a8-9108-9d2c18087a13)
![Screenshot 2025-04-27 151956](https://github.com/user-attachments/assets/4cfbd3a5-96a2-45b8-bd34-9cd4369524e2)



### Terraform commands to execute the script

```sh
# initialise project & download providers
terraform init

# preview what will be created with apply & see if any errors
terraform plan

# exeucute with preview
terraform apply -var-file terraform.tfvars

# execute without preview
terraform apply -var-file terraform.tfvars -auto-approve

# destroy everything
terraform destroy

# show resources and components from current state
terraform state list
```

### Get access to EKS cluster
```sh
# install and configure awscli with access creds
aws configure

# check existing clusters list
aws eks list-clusters --region eu-central-1 --output table --query 'clusters'

# check config of specific cluster - VPC config shows whether public access enabled on cluster API endpoint
aws eks describe-cluster --region eu-central-1 --name myapp-eks-cluster --query 'cluster.resourcesVpcConfig'

# create kubeconfig file for cluster in ~/.kube
aws eks update-kubeconfig --region eu-central-1 --name myapp-eks-cluster

# test configuration
kubectl get svc
```
