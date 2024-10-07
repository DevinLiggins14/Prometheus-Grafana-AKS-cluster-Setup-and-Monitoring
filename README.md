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

##  Step 2: Install Prometheus and Grafana

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



##  Step 3: Access the Pormetheus and Grafana UI 

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
kubectl port-forward svc/grafana 8080:80
```
<img src="https://github.com/user-attachments/assets/3240e699-6099-4c23-9e41-a5d287d88209"/>
<img src="https://github.com/user-attachments/assets/1ddff298-7918-40fe-80ba-c8320f0b5b38"/>


## Step 4: Login to Grafana 
```Bash
# To gather the credentials to login to Grafana
kubectl get secret --namespace default grafana -o yaml
```
<img src="https://github.com/user-attachments/assets/05ca6d30-26e1-4ba6-bf3c-ae975c59a114"/>
</br> However as you can see the contents of the admin user and password are encrypted  

To decrypt run the following:
<br/>
```bash
# Print the decrypted Grafana password
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
````

<br/> Note the Grafana username will be always be admin by default and once logged in the password can now be changed <br/>
<img src="https://github.com/user-attachments/assets/93417c1f-bfe3-4aac-8713-46114fc2c54d"/>
<img src="https://github.com/user-attachments/assets/abfbba53-de5c-40c7-af5f-668a188faf12"/>


### Step 5: Add the Prometheus Database 

<br/> From within Grafana naviagte to connections and select prometheus<br/>
<img src="https://github.com/user-attachments/assets/2db3008b-9fe8-4dfe-9f84-3ed88176fd29"/>

<br/> Next click add new data source and then enter the prometheus server address <br/>
<img src="https://github.com/user-attachments/assets/78d78662-1db1-4480-924f-b8a38a22dba0"/>
<img src="https://github.com/user-attachments/assets/6688d363-c43a-401d-a330-9853f03315a7"/>
</br> Note: this error stems from the fact that running Grafana and Prometheus together in different container environments, each localhost refers to its own container - if the server URL is localhost:9090, that means port 9090 inside the Grafana container, not port 9090 on the host machine.<br/>

<br/> More info on this can be found at https://grafana.com/docs/grafana/latest/datasources/prometheus/configure-prometheus-data-source/ <br/> 

<br/> The entry for the prometheus server should look like this: <br/>
<img src="https://github.com/user-attachments/assets/6ddc308f-8f68-4b04-b836-4e27662e901b"/>
<br/> Result: <br/>
<img src="https://github.com/user-attachments/assets/cb63ed2f-49e2-4c67-8b90-7db22b358260"/>

### **Step 6 Create a dashboard**

<br/> We can now display the metrics being scraped by Prometheus. To do that we must create a dashboard, for this demo we will import one. Navigate to dashboards and select import <br/>
<img src="https://github.com/user-attachments/assets/9f8a56a4-3581-4c58-935f-8b06003eba63"/>

</br> Next click on the grafana dashboards link and find the dashboard titled Kubernetes Cluster monitoring via Prometheus  <br/>

<img src="https://github.com/user-attachments/assets/54f0e8ce-caad-46ef-96ff-ed6db2c0b844"/>
<br/> Click copy ID to clipboard and paste it into grafana <br/>
<img src="https://github.com/user-attachments/assets/48da64ee-6b0e-4bab-aedc-0ad6662395f3"/>
<br/> Next select the prometheus data source<br/>
<img src="https://github.com/user-attachments/assets/052f60ef-5f2b-4ab6-8a9d-916665aee942"/>
<br/> Select import and now the dashboard will be displayed with the prometheus metrics being shown from the AKS cluster <br/>
<img src="https://github.com/user-attachments/assets/9c891355-1320-4baa-b25c-8fee5a9aae94"/>
<img src="https://github.com/user-attachments/assets/17a84a3c-1f6e-4633-8176-5c3ebbba1fc9"/>
<br/> Next click save and navigate to alerting. Next select the prometheus data source and the dashbaord we just created <br/>
<img src="https://github.com/user-attachments/assets/ad5c4847-00cb-4dd7-8f2c-ef25a5be4d8a"/>
<br/> Then you can configure alerts and the contact points for where you will be notified <br/>
<img src="https://github.com/user-attachments/assets/bc5ea2ec-99c2-4207-ae60-5ebdc02e5d91"/>
