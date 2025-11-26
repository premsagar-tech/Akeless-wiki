# Chapter 2 Lab – Architecture Understanding (Hands-On Exploration)

## 🎯 Objective
Understand the internal architecture of AKS + Akeyless + ESO using command-line tools and architecture exploration.  
This lab does **not** deploy resources yet—it deepens your architecture knowledge.

---

## 1. Explore Kubernetes Service Account Tokens

### Steps:
1. Create a dummy ServiceAccount:
   ```
   kubectl create sa demo-sa
   ```
2. Describe the SA:
   ```
   kubectl describe sa demo-sa
   ```
3. Note how tokens are automatically mounted into pods.

---

## 2. View Kubernetes JWT Identity Flow

1. Run a debug pod:
   ```
   kubectl run jwt-test --image=busybox -it -- sh
   ```
2. Inside pod, view token:
   ```
   cat /var/run/secrets/kubernetes.io/serviceaccount/token
   ```
3. Decode token:
   ```
   TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
   echo $TOKEN | base64 -d
   ```

This helps you understand what ESO sends to Akeyless.

---

## 3. Explore ESO CRDs Installed in Cluster

Run:
```
kubectl api-resources | grep external
```

Typical output:
- ExternalSecret
- SecretStore
- ClusterSecretStore

---

## 4. Study Syncing Behavior (Simulated)

Create a dummy ExternalSecret:

```
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: dummy-sync
spec:
  refreshInterval: 10s
  secretStoreRef:
    name: dummy-store
    kind: SecretStore
  data:
  - secretKey: test
    remoteRef:
      key: /dummy/path
```

Then run:
```
kubectl describe externalsecret dummy-sync
```

Observe:
- Sync events  
- Error logs  
- Value resolution  

---

## 5. Architecture Understanding Checkpoints

You should now understand:
✔ How Kubernetes identity is represented in tokens  
✔ How ESO validates and uses K8s authentication  
✔ How CRDs enable the external secret model  
✔ How refresh intervals control sync frequency  

---

## ✅ Lab Complete
You are ready to move to Chapter 3 – Deployment Lab.
