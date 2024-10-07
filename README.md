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
helm install grafana grafana/grafana
kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-ext

```
<br/> Make sure Helm is installed <br/>
<img src="https://github.com/user-attachments/assets/885dd9ee-d29c-4a5b-b9cd-11eae0e521de"/>
<img src="https://github.com/user-attachments/assets/cd8b6112-3518-47a8-b3eb-a9b0a9ca0700"/>

<br/> Confirm the repos and then confirm install <br/>
<img src="https://github.com/user-attachments/assets/8fbea5a6-e59b-46ca-8ed1-3e4a6c93fbf1"/>
<img src="https://github.com/user-attachments/assets/bbeb6bbc-b1fa-4608-8191-bac10b07d2a9"/>
<img src="https://github.com/user-attachments/assets/be8e2f20-462e-4cb7-aec8-9deb7ab5a428"/>



## 🚀 Step 3: Access the Pormetheus and Grafana UI 

<br/> If after creating the NodePort service the Prometheus and Grafana UI is inaccessible, this could be due to the fact that Azure security groups or network rules to allow inbound traffic to the NodePort from your IP have not been configured <br/>

<br/> To fix this you must navigate to Log in to the Azure Portal.
Navigate to your AKS cluster and find the node resource group.
Locate the Network Security Group (NSG) for your nodes.
Add an inbound security rule for the NodePort range.<br/>

<img src="https://github.com/user-attachments/assets/31503d76-c470-4714-a36f-d577a8e9c53e"/>

<br/> Another solution to access the Prometheus and Grafana UI is to use port forwarding   

For the demo you can use, you can use kubectl port-forward:
```bash
kubectl port-forward svc/prometheus-server 9090:80
```
This way, http://localhost:9090 will show you the prometheus ui screen because you have kubectl access.

<br/>

<img src="https://github.com/user-attachments/assets/2e27ac24-e4a7-4a73-b6c5-1cacaf2f47f7"/>
<img src="https://github.com/user-attachments/assets/afe44fce-876d-4c4b-adb1-51c74c4bf9c8"/>
<br/> Confirm its contents </br>
<img src="https://github.com/user-attachments/assets/2acd78cf-95fe-4800-b65c-ad57a0ca374e"/>


### ***Now do the same for Grafana**

```bash
kubectl port-forward svc/grafana 3000:80

```
<img src=""/>



## ✅ Step 4: Verify the Installation
```Bash
kubectl get all -n monitoring
```

