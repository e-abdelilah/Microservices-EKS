# 🛒 E-Commerce Microservices on AWS EKS

[![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Jenkins](https://img.shields.io/badge/jenkins-%232C5263.svg?style=for-the-badge&logo=jenkins&logoColor=white)](https://www.jenkins.io/)
[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)

A production-ready, fully automated CI/CD pipeline for deploying an 11-microservice e-commerce application on AWS EKS using Jenkins.

---

## 📸 Project Overview


This project demonstrates a complete end-to-end deployment of a microservices-based e-commerce platform with:
- **11 Independent Microservices** running on Kubernetes
- **Automated CI/CD Pipeline** using Jenkins Multibranch
- **Container Orchestration** with AWS EKS
- **Service Discovery** via Kubernetes DNS
- **Load Balancing** with AWS Elastic Load Balancer
- **Auto-healing** and **Auto-scaling** capabilities

---

### CI/CD Pipeline Flow

### System Architecture
<img width="1427" height="700" alt="image" src="https://github.com/user-attachments/assets/a0eea496-867e-4e96-9951-983aea373563" />


<img width="1404" height="712" alt="image" src="https://github.com/user-attachments/assets/b136f142-70a5-42a3-a291-f59f5f4df293" />


```
┌─────────────────────────────────────────────────────┐
│                   End Users                          │
└──────────────────┬──────────────────────────────────┘
                   │ HTTPS/HTTP
                   ▼
         ┌─────────────────────┐
         │  AWS Load Balancer  │  (ELB)
         └──────────┬──────────┘
                    │
         ┌──────────▼──────────┐
         │   Frontend Service  │  (Port 8080)
         └──────────┬──────────┘
                    │
      ┌─────────────┼─────────────────┐
      │             │                 │
      ▼             ▼                 ▼
┌──────────┐  ┌──────────┐     ┌──────────┐
│ Product  │  │   Cart   │     │    Ad    │
│ Catalog  │  │ Service  │     │ Service  │
└────┬─────┘  └────┬─────┘     └──────────┘
     │             │
     │        ┌────▼────┐
     │        │  Redis  │
     │        └─────────┘
     │
     └──► + Payment, Shipping, Email, 
           Checkout, Currency, 
           Recommendation Services
```

### Web App
<img width="1100" height="664" alt="image5" src="https://github.com/user-attachments/assets/9656d5f7-2a00-4e1c-b7fa-4d7ae51f1a75" />


### Jenkins Multibranch Pipelines 
<img width="1307" height="643" alt="jobs" src="https://github.com/user-attachments/assets/4cc25e52-0936-4ce2-9f9c-b05a2dd54119" />

---

## 🚀 Microservices

| Service | Language | Port | Description |
|---------|----------|------|-------------|
| **frontend** | Go | 8080 | Web UI and entry point |
| **cartservice** | C# | 7070 | Shopping cart management |
| **productcatalogservice** | Go | 3550 | Product inventory |
| **currencyservice** | Node.js | 7000 | Currency conversion |
| **paymentservice** | Node.js | 50051 | Payment processing (gRPC) |
| **shippingservice** | Go | 50051 | Shipping calculation (gRPC) |
| **emailservice** | Python | 5000 | Order confirmation emails |
| **checkoutservice** | Go | 5050 | Checkout orchestration |
| **recommendationservice** | Python | 8080 | ML-based recommendations |
| **adservice** | Java | 9555 | Contextual advertisements |
| **redis-cart** | Redis | 6379 | Session/cart storage |
| **loadgenerator** | Python | - | Traffic simulation |

---

## 📋 Prerequisites

Before you begin, ensure you have:

- ✅ **AWS Account** with appropriate permissions
- ✅ **GitHub Account** to fork the repository
- ✅ **Docker Hub Account** for container registry
- ✅ Basic understanding of Kubernetes and CI/CD concepts

---

## 🛠️ Technology Stack

**Cloud Infrastructure:**
- AWS EKS (Elastic Kubernetes Service)
- AWS EC2 (Jenkins Server)
- AWS ELB (Elastic Load Balancer)
- AWS IAM (Identity and Access Management)

**Container & Orchestration:**
- Docker & Docker Hub
- Kubernetes 1.28+
- kubectl, eksctl

**CI/CD:**
- Jenkins (Multibranch Pipeline)
- GitHub Webhooks


## 📦 Installation & Setup

### Step 1: Fork and Clone Repository

```bash
# Fork this repository on GitHub, then clone it
git clone https://github.com/e-abdelilah/Microservices-EKS.git
cd Microservices-EKS

# Add upstream remote (if needed)
git remote add upstream https://github.com/e-abdelilah/Microservices-EKS.git
```

### Step 2: AWS Infrastructure Setup

#### 2.1 Create IAM User

1. Go to AWS Console → IAM → Users → Create User
2. Attach the following policies:
   - `AmazonEC2FullAccess`
   - `AmazonEKS_CNI_Policy`
   - `AmazonEKSClusterPolicy`
   - `AmazonEKSWorkerNodePolicy`
   - `AWSCloudFormationFullAccess`
   - `IAMFullAccess`

3. Create inline policy for EKS:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "eks:*",
            "Resource": "*"
        }
    ]
}
```
<img width="1115" height="437" alt="image" src="https://github.com/user-attachments/assets/dbfae390-38be-495f-afd2-d4f2b6861936" />


4. Generate Access Keys (save them securely)

#### 2.2 Create Security Group

1. Go to EC2 → Security Groups → Create Security Group
2. Add inbound rules:
 <img width="706" height="287" alt="image14" src="https://github.com/user-attachments/assets/822fb676-6274-41f2-bdbc-88fdfa955546" />


#### 2.3 Launch EC2 Instance for Jenkins

```bash
# Instance specifications:
- AMI: Ubuntu 22.04 LTS
- Instance Type: t2.large (minimum)
- Storage: 30 GB gp3
- Security Group: Use the one created above
- Key Pair: Create or use existing
```

### Step 3: Configure EC2 Instance

SSH into your EC2 instance:

```bash
ssh -i your-key.pem ubuntu@<EC2-PUBLIC-IP>
```

#### 3.1 Install CLI Tools

Create installation script:

```bash
mkdir -p ~/scripts
cd ~/scripts
nano install_tools.sh
```

Add the following content:

```bash
#!/bin/bash

# Update system
sudo apt-get update -y

# Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip -y
unzip awscliv2.zip
sudo ./aws/install
aws --version

# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client

# Install eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

echo "All tools installed successfully!"
```

Run the script:

```bash
chmod +x install_tools.sh
./install_tools.sh
```

#### 3.2 Configure AWS CLI

```bash
aws configure
# Enter your AWS Access Key ID
# Enter your AWS Secret Access Key
# Default region: eu-north-1 (or your preferred region)
# Default output format: json
```

### Step 4: Create EKS Cluster

#### 4.1 Create Cluster Control Plane

```bash
eksctl create cluster \
  --name microservices-cluster \
  --region eu-north-1 \
  --zones eu-north-1a,eu-north-1b \
  --without-nodegroup
```

**Note:** This takes approximately 15-20 minutes.

#### 4.2 Associate IAM OIDC Provider

```bash
eksctl utils associate-iam-oidc-provider \
  --region eu-north-1 \
  --cluster microservices-cluster \
  --approve
```

#### 4.3 Create Node Group

```bash
eksctl create nodegroup \
  --cluster=microservices-cluster \
  --region=eu-north-1 \
  --name=microservices-nodes \
  --node-type=t3.medium \
  --nodes=2 \
  --nodes-min=2 \
  --nodes-max=4 \
  --node-volume-size=20 \
  --ssh-access \
  --ssh-public-key=your-key-name \
  --managed \
  --asg-access \
  --external-dns-access \
  --full-ecr-access \
  --appmesh-access \
  --alb-ingress-access
```

Verify cluster:

```bash
kubectl get nodes
kubectl cluster-info
```

### Step 5: Install and Configure Jenkins

#### 5.1 Install Java and Jenkins

```bash
# Install Java
sudo apt update
sudo apt install openjdk-17-jre-headless -y
java -version

# Add Jenkins repository
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | \
  sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Install Jenkins
sudo apt-get update
sudo apt-get install jenkins -y

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins
```

#### 5.2 Install Docker

```bash
# Install Docker
sudo apt install docker.io -y

# Add Jenkins and current user to docker group
sudo usermod -aG docker jenkins
sudo usermod -aG docker $USER

# Fix permissions
sudo chmod 666 /var/run/docker.sock

# Restart Jenkins
sudo systemctl restart jenkins
```

#### 5.3 Access Jenkins

1. Get initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

2. Open browser: `http://<EC2-PUBLIC-IP>:8080`
3. Enter the initial admin password
4. Install suggested plugins
5. Create admin user

#### 5.4 Install Jenkins Plugins

Go to: **Manage Jenkins** → **Plugins** → **Available Plugins**

Install:
- Docker
- Docker Pipeline
- Kubernetes
- Kubernetes CLI
- Multibranch Scan Webhook Trigger

#### 5.5 Configure Docker in Jenkins

**Manage Jenkins** → **Tools** → **Docker installations**
- Name: `docker`
- Install automatically: ✅ Check
- Docker version: `latest`

### Step 6: Setup Kubernetes RBAC

Create namespace and service account:

```bash
# Create namespace
kubectl create namespace webapps

# Create service account
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
EOF
```

Create role with permissions:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
EOF
```

Bind role to service account:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role
subjects:
  - kind: ServiceAccount
    name: jenkins
    namespace: webapps
EOF
```

Create service account token:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  namespace: webapps
  annotations:
    kubernetes.io/service-account.name: jenkins
EOF
```

Get the token:

```bash
kubectl describe secret mysecretname -n webapps
# Copy the token (it's a long string)
```

### Step 7: Configure Jenkins Credentials

#### 7.1 Add Docker Hub Credentials

**Manage Jenkins** → **Credentials** → **System** → **Global credentials** → **Add Credentials**

- Kind: `Username with password`
- Username: Your Docker Hub username
- Password: Your Docker Hub password
- ID: `docker-cred`
- Description: `Docker Hub Credentials`

#### 7.2 Add Kubernetes Token

**Add Credentials** again:

- Kind: `Secret text`
- Secret: Paste the token from previous step
- ID: `k8s-token`
- Description: `Kubernetes Token`

#### 7.3 Add GitHub Credentials (Optional)

If your repository is private:

- Kind: `Username with password`
- Username: Your GitHub username
- Password: Your GitHub Personal Access Token
- ID: `git-cred`
- Description: `GitHub Credentials`

### Step 8: Update Jenkinsfiles

For each service branch, update the Jenkinsfile:

1. Update Docker Hub username:
```groovy
// Change this line in all service branch Jenkinsfiles
sh "docker build -t YOUR-DOCKERHUB-USERNAME/servicename:latest ."
sh "docker push YOUR-DOCKERHUB-USERNAME/servicename:latest"
```

2. Update master branch Jenkinsfile:
```groovy
withKubeCredentials(kubectlCredentials: [[
    caCertificate: '', 
    clusterName: 'microservices-cluster',  // Your cluster name
    contextName: '', 
    credentialsId: 'k8s-token', 
    namespace: 'webapps', 
    serverUrl: 'https://YOUR-EKS-API-ENDPOINT'  // Get from: kubectl cluster-info
]])
```

Get EKS API endpoint:
```bash
kubectl cluster-info | grep "Kubernetes control plane"
```

### Step 9: Configure GitHub Webhook

1. Go to your GitHub repository
2. **Settings** → **Webhooks** → **Add webhook**
3. Payload URL: `http://<EC2-PUBLIC-IP>:8080/multibranch-webhook-trigger/invoke?token=microservices`
4. Content type: `application/json`
5. Events: `Just the push event`
6. Active: ✅ Check
7. Add webhook

### Step 10: Create Jenkins Multibranch Pipeline

1. Jenkins Dashboard → **New Item**
2. Name: `MicroService-Ecommerce`
3. Type: **Multibranch Pipeline**
4. **Branch Sources** → **Add source** → **Git**
5. Project Repository: `https://github.com/YOUR-USERNAME/Microservices-EKS.git`
6. Credentials: Select `git-cred` (if private repo)
7. **Scan Multibranch Pipeline Triggers**:
   - ✅ Scan by webhook
   - Trigger token: `microservices`
8. **Save**

Jenkins will automatically scan all branches and create pipelines.

### Step 11: Deploy to Kubernetes

The master branch pipeline will automatically deploy all services. Monitor the deployment:

```bash
# Watch pods
kubectl get pods -n webapps -w

# Check services
kubectl get svc -n webapps

# Get LoadBalancer URL
kubectl get svc frontend-external -n webapps
```

Wait until all pods show `Running` status.

---

## 🌐 Accessing the Application

Once deployed, get the LoadBalancer URL:

```bash
kubectl get svc frontend-external -n webapps

# Output:
# NAME                TYPE           EXTERNAL-IP
# frontend-external   LoadBalancer   a3ebca8...amazonaws.com
```

Access your application:
```
http://<LOAD-BALANCER-URL>
```

---

## 📊 Monitoring and Verification

### Check Pod Status

```bash
# Get all pods
kubectl get pods -n webapps

# Describe specific pod
kubectl describe pod <POD-NAME> -n webapps

# View pod logs
kubectl logs <POD-NAME> -n webapps

# Follow logs in real-time
kubectl logs -f <POD-NAME> -n webapps
```

### Check Services

```bash
# Get all services
kubectl get svc -n webapps

# Check endpoints
kubectl get endpoints -n webapps

# Describe LoadBalancer
kubectl describe svc frontend-external -n webapps
```

### Check Deployments

```bash
# Get all deployments
kubectl get deployments -n webapps

# Check rollout status
kubectl rollout status deployment/frontend -n webapps

# View deployment history
kubectl rollout history deployment/frontend -n webapps
```

### Resource Usage

```bash
# Get node resources
kubectl top nodes

# Get pod resources (requires metrics-server)
kubectl top pods -n webapps
```

---

## 🐛 Troubleshooting

### Common Issues and Solutions

#### Issue 1: Pods in CrashLoopBackOff

**Symptoms:**
```bash
NAME                     READY   STATUS             RESTARTS
frontend-xxx             0/1     CrashLoopBackOff   5
```

**Diagnosis:**
```bash
# Check pod logs
kubectl logs <POD-NAME> -n webapps

# Check pod events
kubectl describe pod <POD-NAME> -n webapps
```

**Common Causes:**
- Missing environment variables
- Incorrect service addresses
- Container image issues
- Resource constraints

**Solution:**
```bash
# Delete pod to force recreation
kubectl delete pod <POD-NAME> -n webapps

# Or restart deployment
kubectl rollout restart deployment/<DEPLOYMENT-NAME> -n webapps
```

#### Issue 2: Service Endpoints Empty

**Symptoms:**
```bash
kubectl get endpoints frontend -n webapps
# Shows no endpoints
```

**Diagnosis:**
```bash
# Check if pods are ready
kubectl get pods -l app=frontend -n webapps

# Check service selector
kubectl describe svc frontend -n webapps
```

**Solution:**
- Ensure pods are in Running state
- Verify service selector matches pod labels
- Check readiness probes

#### Issue 3: LoadBalancer Pending

**Symptoms:**
```bash
frontend-external   LoadBalancer   <pending>
```

**Diagnosis:**
```bash
kubectl describe svc frontend-external -n webapps
```

**Solution:**
- Wait 2-3 minutes for AWS to provision ELB
- Check AWS ELB console for any issues
- Verify VPC and subnet configuration

#### Issue 4: Cannot Access Application

**Diagnosis:**
```bash
# Test from inside cluster
kubectl run test-pod --rm -i --tty --image=curlimages/curl -- sh
curl http://frontend:80
```

**Solution:**
- Check Security Group allows port 80
- Verify LoadBalancer is in "active" state
- Test health endpoint: `curl http://<LB-URL>/_healthz`

#### Issue 5: Jenkins Docker Permission Denied

**Symptoms:**
```
Got permission denied while trying to connect to Docker daemon
```

**Solution:**
```bash
# On EC2 instance
sudo usermod -aG docker jenkins
sudo chmod 666 /var/run/docker.sock
sudo systemctl restart jenkins
```

#### Issue 6: kubectl Unable to Connect

**Symptoms:**
```
The connection to the server localhost:8080 was refused
```

**Solution:**
```bash
# Update kubeconfig
aws eks update-kubeconfig --region eu-north-1 --name microservices-cluster

# Verify
kubectl get nodes
```

### Debug Commands Cheatsheet

```bash
# View all resources
kubectl get all -n webapps

# Get events
kubectl get events -n webapps --sort-by='.lastTimestamp'

# Exec into pod
kubectl exec -it <POD-NAME> -n webapps -- /bin/sh

# Port forward to local machine
kubectl port-forward svc/frontend 8080:80 -n webapps

# Check resource quotas
kubectl describe resourcequota -n webapps

# View cluster info
kubectl cluster-info dump
```

---

## 🔄 Making Changes and Updates

### Update a Single Service

1. Make changes to service code
2. Commit and push to service branch:
```bash
git add .
git commit -m "Update service"
git push origin <service-branch>
```

3. Jenkins will automatically:
   - Build new Docker image
   - Push to Docker Hub
   - Trigger master branch to redeploy

### Update Deployment Configuration

1. Edit `deployment-service.yml`
2. Commit to master branch
3. Jenkins will apply changes automatically

Or manually apply:
```bash
kubectl apply -f deployment-service.yml
```

### Rollback Deployment

```bash
# View rollout history
kubectl rollout history deployment/frontend -n webapps

# Rollback to previous version
kubectl rollout undo deployment/frontend -n webapps

# Rollback to specific revision
kubectl rollout undo deployment/frontend --to-revision=2 -n webapps
```

---

## 🔐 Security Best Practices

✅ **Implemented:**
- RBAC with limited service account permissions
- Non-root containers where possible
- Read-only root filesystem
- Security contexts with dropped capabilities
- Secrets management for sensitive data
- Private subnets for worker nodes

⚠️ **Recommended Additions:**
- Enable Pod Security Policies/Standards
- Implement Network Policies
- Use AWS Secrets Manager for sensitive data
- Enable audit logging
- Implement image scanning in CI/CD
- Use private container registry
- Enable encryption at rest

---

## 📈 Performance Optimization

### Resource Limits

Each service has defined resource limits:

```yaml
resources:
  requests:
    cpu: 100m
    memory: 64Mi
  limits:
    cpu: 200m
    memory: 128Mi
```

### Horizontal Pod Autoscaling

Enable HPA for services:

```bash
kubectl autoscale deployment frontend \
  --cpu-percent=70 \
  --min=2 \
  --max=10 \
  -n webapps
```

### Cluster Autoscaling

Already enabled with:
- Min nodes: 2
- Max nodes: 4
- Node type: t3.medium


## 🧹 Cleanup

### Delete Everything

```bash
# Delete Kubernetes resources
kubectl delete namespace webapps

# Delete EKS Node Group
eksctl delete nodegroup \
  --cluster=microservices-cluster \
  --region=eu-north-1 \
  --name=microservices-nodes

# Delete EKS Cluster
eksctl delete cluster \
  --name=microservices-cluster \
  --region=eu-north-1

# Terminate EC2 Instance (via AWS Console)
# Delete Security Groups (via AWS Console)
# Delete IAM User/Roles (via AWS Console)
```

⚠️ **Warning:** This will permanently delete all resources!



## 📚 Learning Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [AWS EKS Best Practices](https://aws.github.io/aws-eks-best-practices/)
- [Jenkins Pipeline Documentation](https://www.jenkins.io/doc/book/pipeline/)
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [Microservices Patterns](https://microservices.io/patterns/)

---


## 👤 Me

- GitHub: [@e-abdelilah](https://github.com/e-abdelilah)
- LinkedIn: [Abdelilah ETTARCH](https://www.linkedin.com/in/abdelilahettarch/)
- Email: ettarch.abdelilah@gmail.com



---

