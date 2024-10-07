# Prometheus-Grafana-AKS-cluster-Setup-and-Monitoring
<h2>Description</h2>
In this projet we will set up Prometheus and Grafana to monitor and create alerts for an AKS Cluster
<br />
<br/>  Prometheus architecture: <br/>
<img src="https://github.com/user-attachments/assets/4bf3b677-2866-4a42-ad94-8e7776117baa"/>


<h2>Languages and Utilities Used</h2>

Bash, Az-cli, Kubectl, VSCode, Helm (Optional: cloudshell)

<h2>Environments Used </h2>

- <b>CentOS Linux release 8.5.2111
 10, Azure portal </b>

<h2>Project walk-through:</h2>
<br/>
<p align="center">

 ## 📦 Step 1: Create AKS Cluster

### **Prerequisites**  
- Download and Install [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).  
- Setup and configure Azure CLI using the `az login` command.  
- Install and configure `kubectl` as mentioned [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

```bash
# Create a resource group
az group create --name aks-demo-rg --location eastus

# Create the AKS cluster
az aks create --resource-group aks-demo-rg --name aks-demo --location eastus2 --node-count 2 --enable-managed-identity --generate-ssh-keys
```

<img src="https://github.com/user-attachments/assets/ac86358c-104c-48b6-a506-a0d3658833bf"/>
<img src="https://github.com/user-attachments/assets/2724b032-6e8a-45b1-ad2d-5554ba06f801"/>

### **Connect to AKS Cluster**  
```bash
# Login to your azure account
az login
# Set the cluster subscription
az account set --subscription Enter subscription Here
# Download cluster credentials
az aks get-credentials --resource-group aks-demo-rg --name aks-demo --overwrite-existing

```
<img src="https://github.com/user-attachments/assets/3c3af067-95d3-458f-a90e-e046da1dcdac"/>

<br/>  Make sure clock is syncronized and confirm <br/>

<img src="https://github.com/user-attachments/assets/ec68a4be-19ad-4a44-8332-229853a4b662"/>

## 🧰 Step 2: Install Prometheus and Grafana

```bash
# Steps to Install Prometheus:
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus
kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext

# Steps to install Grafana:
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana stable/grafana
kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-ext

```

<img src=""/>

## 🚀 Step 3: Deploy Prometheus and Grafana into a new namespace "monitoring"

```bash
# Create a new namespace called "monitoring"
kubectl create ns monitoring

# Navigate to the appropriate directory (if applicable)
cd monitoring-dir

# Install the kube-prometheus-stack into the "monitoring" namespace
helm install monitoring prometheus-community/kube-prometheus-stack \
-n monitoring \
-f ./custom_kube_prometheus_stack.yml
```
<img src=""/>
## ✅ Step 4: Verify the Installation
```Bash
kubectl get all -n monitoring
```

