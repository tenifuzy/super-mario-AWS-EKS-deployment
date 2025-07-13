# Super Mario AWS EKS Deployment

A complete containerized Super Mario Bros web application deployed on AWS EKS (Elastic Kubernetes Service) using Terraform for infrastructure as code and Kubernetes manifests for application deployment.

## 🎮 Project Overview

This project demonstrates modern DevOps practices by deploying a classic Super Mario Bros game as a web application on AWS cloud infrastructure. The deployment leverages:

- **AWS EKS** for managed Kubernetes orchestration
- **Terraform** for infrastructure provisioning
- **Docker** containerization with pre-built image
- **Kubernetes** manifests for application deployment
- **LoadBalancer** service for external access

## 🏗️ Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Internet      │────│  LoadBalancer    │────│   EKS Cluster   │
│   Users         │    │   Service        │    │                 │
└─────────────────┘    └──────────────────┘    │  ┌───────────┐  │
                                                │  │   Pod 1   │  │
                                                │  │  (Mario)  │  │
                                                │  └───────────┘  │
                                                │  ┌───────────┐  │
                                                │  │   Pod 2   │  │
                                                │  │  (Mario)  │  │
                                                │  └───────────┘  │
                                                └─────────────────┘
```

## 📁 Project Structure

```
super-mario-AWS-EKS-deployment/
├── EKS-TF/
│   ├── main.tf          # EKS cluster and node group configuration
│   └── provider.tf      # Terraform AWS provider configuration
├── deployment.yaml      # Kubernetes deployment manifest
├── service.yaml        # Kubernetes LoadBalancer service
├── script.sh          # Installation script for dependencies
└── README.md          # Project documentation
```

## 🛠️ Prerequisites

Before deploying this application, ensure you have:

1. **AWS Account** with appropriate permissions
2. **AWS CLI** configured with credentials
3. **Terraform** (>= 1.0)
4. **kubectl** for Kubernetes management
5. **Docker** (optional, for local testing)

## 🚀 Quick Setup

Run the provided installation script to install all dependencies:

```bash
chmod +x script.sh
./script.sh
```

Or install manually:

### Manual Installation

#### Install Terraform
```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform -y
```

#### Install kubectl
```bash
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

#### Install AWS CLI
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

## 🔧 Configuration

### AWS Credentials
Configure your AWS credentials:
```bash
aws configure
```

### Terraform Variables
The infrastructure is configured for:
- **Region**: `eu-west-2` (London)
- **Instance Type**: `t2.medium`
- **Node Group**: 1-2 nodes (auto-scaling)

## 📦 Deployment Instructions

### Step 1: Deploy Infrastructure

1. Navigate to the Terraform directory:
```bash
cd EKS-TF
```

2. Initialize Terraform:
```bash
terraform init
```

3. Plan the deployment:
```bash
terraform plan
```

4. Apply the infrastructure:
```bash
terraform apply
```

### Step 2: Configure kubectl

Update your kubeconfig to connect to the EKS cluster:
```bash
aws eks update-kubeconfig --region eu-west-2 --name EKS_CLOUD
```

### Step 3: Deploy Application

1. Deploy the Mario application:
```bash
kubectl apply -f deployment.yaml
```

2. Create the LoadBalancer service:
```bash
kubectl apply -f service.yaml
```

### Step 4: Access the Application

Get the LoadBalancer URL:
```bash
kubectl get service mario-service
```

Wait for the `EXTERNAL-IP` to be assigned, then access the game via your browser.

## 🎯 Usage Examples

### Check Deployment Status
```bash
# View pods
kubectl get pods

# View services
kubectl get services

# View deployment details
kubectl describe deployment mario-deployment
```

### Scale the Application
```bash
# Scale to 3 replicas
kubectl scale deployment mario-deployment --replicas=3

# Check scaling status
kubectl get pods -w
```

### View Logs
```bash
# View logs from all pods
kubectl logs -l app=mario

# View logs from specific pod
kubectl logs <pod-name>
```

## 🧪 Testing

### Health Checks
```bash
# Check cluster health
kubectl get nodes

# Check pod health
kubectl get pods -o wide

# Test service connectivity
kubectl port-forward service/mario-service 8080:80
```

### Load Testing
```bash
# Get LoadBalancer URL
LB_URL=$(kubectl get service mario-service -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

# Simple connectivity test
curl -I http://$LB_URL
```

## 🔍 Troubleshooting

### Common Issues

1. **Pods stuck in Pending state**:
   ```bash
   kubectl describe pod <pod-name>
   ```

2. **LoadBalancer not getting external IP**:
   ```bash
   kubectl describe service mario-service
   ```

3. **EKS cluster access issues**:
   ```bash
   aws eks describe-cluster --name EKS_CLOUD --region eu-west-2
   ```

### Cleanup Commands
```bash
# Delete Kubernetes resources
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml

# Destroy infrastructure
cd EKS-TF
terraform destroy
```

## 💰 Cost Optimization

- **EKS Cluster**: ~$0.10/hour
- **t2.medium instances**: ~$0.0464/hour per node
- **LoadBalancer**: ~$0.025/hour

**Estimated monthly cost**: ~$50-70 USD

## 🔒 Security Considerations

- EKS cluster uses IAM roles for service authentication
- Node groups have minimal required permissions
- LoadBalancer provides secure external access
- Container runs with non-root user privileges

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test the deployment
5. Submit a pull request

## 📄 License

This project is licensed under the terms specified in the LICENSE file.

## 🎮 Game Instructions

Once deployed, access the game through your browser:
- Use arrow keys to move Mario
- Spacebar to jump
- Enjoy the classic Super Mario Bros experience!

---

**Happy Gaming! 🎮**
