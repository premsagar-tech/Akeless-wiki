# Chapter 3 – Deployment Guide: AKS + Akeyless + External Secrets Operator (ESO)

## 1. Introduction
This chapter provides the **full hands-on deployment guide** to integrate:
- Azure Kubernetes Service (AKS)
- Akeyless Zero-Knowledge Secrets Platform
- External Secrets Operator (ESO)

This is an enterprise-ready deployment procedure.

---

## 2. Prerequisites

### 2.1 Tools Required
- Azure CLI  
- kubectl  
- Helm 3  
- Akeyless CLI (`akeyless`)  
- Akeyless account  
- AKS cluster (Basic or Production)  

---

## 3. Step-by-Step Deployment

## 3.1 Step 1 – Create AKS Cluster
```
az group create -n aks-rg -l eastus

az aks create \
  -g aks-rg \
  -n aks-demo \
  --node-count 2 \
  --generate-ssh-keys
```

Get credentials:
```
az aks get-credentials -g aks-rg -n aks-demo
```

---

## 3.2 Step 2 – Install External Secrets Operator (ESO)

Add Helm repo:
```
helm repo add external-secrets https://charts.external-secrets.io
helm repo update
```

Install:
```
helm install eso external-secrets/external-secrets \
  -n external-secrets --create-namespace
```

Verify:
```
kubectl get pods -n external-secrets
```

---

## 3.3 Step 3 – Configure Akeyless

### 3.3.1 Login
```
akeyless login --auth-type apiKey
```

### 3.3.2 Create Auth Method for Kubernetes
```
akeyless auth-method create-k8s \
  --name aks-auth \
  --issuer <AKS_OIDC_ISSUER> \
  --audience https://kubernetes.default.svc
```

Retrieve AKS OIDC issuer:
```
az aks show -g aks-rg -n aks-demo --query "oidcIssuerProfile.issuerUrl" -o tsv
```

---

## 3.4 Step 4 – Create Akeyless Access Role
```
akeyless access-role create --name aks-role
akeyless access-role assoc-auth-method \
  --auth-method-name aks-auth \
  --role-name aks-role
```

---

## 3.5 Step 5 – Grant Secret Permissions
```
akeyless access-role assoc-target \
  --role-name aks-role \
  --target-name /apps/db/password
```

---

## 3.6 Step 6 – Create SecretStore in Kubernetes
```
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: akeyless-store
spec:
  provider:
    akeyless:
      auth:
        k8sAuth:
          accessID: "<YOUR_ACCESS_ID>"
      apiUrl: "https://api.akeyless.io"
```

Apply:
```
kubectl apply -f secretstore.yaml
```

---

## 3.7 Step 7 – Create ExternalSecret
```
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: db-password
spec:
  secretStoreRef:
    name: akeyless-store
    kind: SecretStore
  refreshInterval: 10s
  target:
    name: db-secret
  data:
  - secretKey: password
    remoteRef:
      key: /apps/db/password
```

Apply:
```
kubectl apply -f externalsecret.yaml
```

Verify:
```
kubectl get secret db-secret -o yaml
```

---

## 3.8 Step 8 – Consume Secret in Deployment
```
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: password
```

---

## 4. End-to-End Validation

1. Update secret in Akeyless:
```
akeyless secret update --name /apps/db/password --value NewPass123
```
2. Wait for sync:
```
kubectl describe externalsecret db-password
```
3. Check updated secret:
```
kubectl get secret db-secret -o yaml
```

ESO will pick up updates automatically.

---

## 5. Summary

You have now deployed:
- AKS  
- Akeyless Auth Method  
- Akeyless Access Role  
- ESO via Helm  
- SecretStore  
- ExternalSecret  
- Application consuming secrets  

This forms the complete production-grade deployment flow.
