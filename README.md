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
1. Clone this repo : https://github.com/MeghabMB/gitops_demo.git
2. Apply the root-app.yaml using kubectl:
   ```
   kubectl apply -f root-app.yaml
   ```

<img width="1919" height="1025" alt="image" src="https://github.com/user-attachments/assets/fb6e3cff-d58b-4807-a349-89fbbb9a82a0" />

