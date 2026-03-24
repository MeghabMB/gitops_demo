## GitOps Deployment using Argo CD

Repository Structure :
```
app_of_apps/
│
├── root-app.yaml
│
├── apps/
│   ├── frontend.yaml
│   ├── sbackend.yaml
│   └── redis.yaml
├── frontend/
│   ├── deployment.yaml
│   └── service.yaml
│
├── backend/
│   ├── deployment.yaml
│   └──service.yaml
│
└── redis/
    ├── deployment.yaml
    └── service.yaml
```
### ArgoCD Installation
Create Namespace
```
k create ns argocd
```

Apply ArgoCD Manifest
```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

#### Access ArgoCD UI

Port-Forward ArgoCD Server service to access the ArgoCD UI
```
 k port-forward svc/argocd-server -n argocd 8080:443
```

URL : https://localhost:8080

#### Get login Credentials

Username: admin
Password:
  
  Linux:
  ```
  kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
  ```

  PowerShell:
```
 $password = kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($password))
```

### Steps
1. Clone this repo : https://github.com/MeghabMB/gitops_demo.git
2. Apply the root-app.yaml inside the app_of_apps folder using kubectl:
   ```
   kubectl apply -f root-app.yaml
   ```

<img width="1919" height="1025" alt="image" src="https://github.com/user-attachments/assets/fb6e3cff-d58b-4807-a349-89fbbb9a82a0" />

## Kube Prometheus Setup

**kube-prometheus-stack** is a Kubernetes-native monitoring bundle that deploys Prometheus, Grafana, and Alertmanager together to collect metrics, visualize system health, and trigger alerts across your cluster.
### Installation

Add Helm Repository and Update:
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update
```

Install kube-prometheus-stack
```
helm install prometheus prometheus-community/kube-prometheus-stack
```

### Accessing Credentials:
To login to Grafana , we need password :

#### Linux:

```
kubectl --namespace <namespace> get secrets prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo
```

#### Powershell:

```
$pass = kubectl -n default get secret prometheus-grafana -o jsonpath="{.data.admin-password}"
[Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($pass))
```

### Access Prometheus

Port-forward Service:
```
k port-forward svc/prometheus-kube-prometheus-prometheus 9090
```

### Access Grafana

Port-forward Service:
```
k port-forward svc/prometheus-grafana 3000:80
```

Login:
URL : http://localhost:3000
Username : admin
Password : retrieved above

### Create a Service Monitor for ArgoCD

Access argo-servicemonitor.yaml

Note :
The `release` label in ServiceMonitor **must match** the label defined in Prometheus:

```
kubectl get prometheus  -o yaml
```

#### Apply Service Monitor

```
k apply -f argo-servicemonitor.yaml
```

Port-forward Prometheus to see it:
```
k port-forward svc/prometheus-kube-prometheus-prometheus 9090
```

Access it:
```
127.0.0.1:9090/targets
```

In targets, 
	You should see argocd-controller-metrics and the state should be UP
	

Now in Query tab ,
	Run the Query : argocd_app_info
	You will get the metrics
	
## Implement AlertManager 

Port-forward the Prometheus Alert-manager service:
```
kubectl port-forward svc/prometheus-kube-prometheus-alertmanager 9093
```
###  Create Prometheus Alert Rules

Apply Alert Rules
```
kubectl apply -f argocd-alerts.yaml
```

Verify Rules
```
kubectl get prometheusrules -n monitoring
```
### Configure Alertmanager

#### Deploy Prometheus MS Teams Connector

##### Add Helm Repo
```
helm repo add prometheus-msteams https://prometheus-msteams.github.io/prometheus-msteams/  
helm repo update
```

##### Install:
```
helm install alertmanager-msteams prometheus-msteams/prometheus-msteams \  
  --namespace monitoring \  
  --set connectors[0].alertmanager="insert_url"
```

##### Deploy the values file to helm:
```
helm upgrade prometheus prometheus-community/kube-prometheus-stack    -f prom-msteams-values.yaml
```

##### Verify The Deployment
```
kubectl get pods -n 
```

####  Create MS Teams Webhook

1. Open Microsoft Teams
2. Go to your channel
3. Click ➝ **Connectors**
4. Add ➝ **Incoming Webhook**
5. Copy the webhook URL