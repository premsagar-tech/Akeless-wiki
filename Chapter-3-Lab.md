# Chapter 3 Lab – Full Deployment Lab: AKS + Akeyless + ESO

## 🎯 Objective
Deploy the full working solution from scratch.

---

## 1. Create an AKS Cluster
```
az group create -n aks-rg -l eastus
az aks create -g aks-rg -n aks-lab --node-count 1 --generate-ssh-keys
az aks get-credentials -g aks-rg -n aks-lab
```

---

## 2. Install ESO
```
helm repo add external-secrets https://charts.external-secrets.io
helm install eso external-secrets/external-secrets \
  -n external-secrets --create-namespace
```

---

## 3. Configure Akeyless Kubernetes Auth Method

1. Retrieve OIDC URL:
```
az aks show -g aks-rg -n aks-lab --query "oidcIssuerProfile.issuerUrl" -o tsv
```

2. Create the auth method:
```
akeyless auth-method create-k8s \
  --name aks-lab-auth \
  --issuer <OIDC_ISSUER> \
  --audience https://kubernetes.default.svc
```

---

## 4. Create Akeyless Secret
```
akeyless secret create --name /lab/app/password --value Passw0rd!
```

---

## 5. Create SecretStore
```
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: akeyless-lab-store
spec:
  provider:
    akeyless:
      apiUrl: https://api.akeyless.io
      auth:
        k8sAuth:
          accessID: <ACCESS_ID>
```

Apply:
```
kubectl apply -f secretstore.yaml
```

---

## 6. Create ExternalSecret
```
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: lab-secret
spec:
  secretStoreRef:
    name: akeyless-lab-store
    kind: SecretStore
  target:
    name: lab-k8s-secret
  data:
  - secretKey: password
    remoteRef:
      key: /lab/app/password
```

Apply:
```
kubectl apply -f externalsecret.yaml
```

---

## 7. Verify Sync
```
kubectl get secret lab-k8s-secret -o yaml
```

---

## 8. Update Secret
```
akeyless secret update --name /lab/app/password --value NewLabPass!
```

Wait 10s and check:
```
kubectl get secret lab-k8s-secret -o yaml
```

---

## ✅ Lab Complete
You have fully deployed AKS + Akeyless + ESO from scratch.
