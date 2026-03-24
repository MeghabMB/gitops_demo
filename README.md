## GitOps Deployment using Argo CD

Repository Structure :
```
gitops_demo/
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

### Steps
1. Clone this repo :
2. Apply the root-app.yaml using kubectl:
   ```
   kubectl apply -f root-app.yaml
   ```

## GitOps Deployment using Argo CD

Repository Structure :
```
gitops_demo/
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
<img width="1919" height="1025" alt="image" src="https://github.com/user-attachments/assets/4ee62d0c-d267-4338-86b1-7122a79f8536" />
